Sure! Here is the complete English translation, keeping the **Markdown structure** so you can directly save it as a `.md` file.

# Practical Instructions – Jensen IoT Platform

## How to Use This Guide

Three simulated sensors send temperature, humidity, and battery level data to a REST API. Your task is to complete the API so that the data is validated, stored in PostgreSQL, and the latest measurement can be retrieved through a Redis cache.

The labels mean:

* **Mandatory** – basic task that must be completed.
* **Reflection** – answer in `docs/reflection.md`.
* **Optional extension** – not required to pass.
* **Check** – a concrete way to verify the result.

Complete the milestones in order. Commit and push regularly to your own fork.

### Estimated Time and Prerequisites

Expect approximately **18–24 hours of work** if you have limited experience with Python. The time may vary depending on your experience with SQL, Docker, and Minikube installation.

You do not need to know how to build a Flask application from scratch. However, before starting, you should be familiar with:

* Python functions, dictionaries, lists, `None`, and imports
* how JSON is received and returned in a REST API
* basic `SELECT`, `INSERT`, `WHERE`, and `ORDER BY` in SQL
* how a simple test using `pytest` is structured

If any of these are new to you, use the course material or ask for guidance before trying to troubleshoot Docker or Kubernetes. Start with `validation.py` and the existing tests; they are the smallest and simplest examples in the codebase.

### Development Cycle When Changing Code

The source code is copied into the Docker image and therefore does not update automatically. After making a code change, rebuild and restart the affected services:

```text
docker compose up --build -d
```

Then check the status and logs:

```text
docker compose ps
docker compose logs --tail=50 api simulator
```

A Python error will normally appear in the API log. A failed request from the simulator will appear in the simulator log.

## Milestone 1 – IoT Data API

### 1. Familiarize Yourself with the Starting Point

**Mandatory:** Start the environment according to [`README.md`](../README.md). Then read:

* `database/init.sql` for tables and initial data
* `api/app.py` for endpoints
* `api/db.py` for database functions
* `api/validation.py` and `api/tests/test_validation.py` for validation and tests

The files contain `TODO M1` markers where code is missing. The following endpoints should work when the milestone is complete:

| Method and Path                  | Task                             | Expected Status                                                    |
| -------------------------------- | -------------------------------- | ------------------------------------------------------------------ |
| `POST /measurements`             | validate and store a measurement | `201` when created, `400` for invalid data or unknown sensor       |
| `GET /measurements`              | list the latest measurements     | `200`                                                              |
| `GET /devices`                   | list sensors                     | `200`                                                              |
| `GET /devices/{id}/measurements` | show a sensor's history          | `200` (also for an empty list), `404` if the sensor does not exist |
| `GET /devices/{id}/latest`       | show the latest measurement      | `200`, `404` if the sensor or measurement does not exist           |

### Python and PostgreSQL Support

You do not need to create the database structure from scratch. Use `get_devices()` and `get_measurements()` in `api/db.py` as examples for connection handling, cursors, and JSON conversion.

Always pass values as parameters instead of building SQL using strings:

```python
cur.execute(
    "SELECT ... FROM ... WHERE device_id = %s",
    (device_id,),
)
row = cur.fetchone()
```

`fetchone()` returns one row or `None`. `fetchall()` returns a list, which can be empty. Use `RETURNING` together with `INSERT` if you need to get the created row back. You should replace `...` with the correct table, columns, and SQL; the example is not a complete solution.

### 2. Implement Data Storage

**Mandatory:**

1. Implement `device_exists(device_id)` in `api/db.py` so that it determines whether the sensor exists in the `devices` table.
2. Implement `insert_measurement(data)` in `api/db.py` using a parameterized `INSERT` query. Return the created row.
3. Call these functions from `POST /measurements` in `api/app.py`.
4. Change the starter response from `202` to `201` only when the measurement has actually been saved.
5. Keep validation before database storage. Invalid measurements must return `400` and must not be stored.
6. Check that `deviceId` exists in the `devices` table before storing the measurement. An unknown ID, such as `sensor-999`, should return `400` with a clear JSON error instead of a database error.

**Check:** Rebuild after code changes and monitor the simulator:

```text
docker compose up --build -d
docker compose logs -f simulator
```

Valid rows should now receive `201`; intentionally invalid rows from `sensor-003` should receive `400`. Open `/measurements` and check that the list is being populated. Exit the log view with `Ctrl+C`.

### 3. Implement History and Latest Measurement

**Mandatory:**

1. Implement `get_measurements_for_device(device_id)` and `get_latest_measurement(device_id)` in `api/db.py`.
2. Use the functions in the corresponding endpoints in `api/app.py`.
3. Distinguish between a known sensor with no measurements and an unknown sensor. History for a known sensor with no measurements should return `200` and `[]`; an unknown sensor ID should return `404`.
4. Return JSON and relevant status codes according to the table above.
5. Add at least three validation tests in addition to the starter tests:

   * missing `deviceId`
   * incorrect data type for `humidity`
   * incorrect data type for `battery`

Database, cache, and endpoint flows are verified through the guide's manual checks. Automated integration tests are an optional extension.

**Check:** Replace `{id}` with, for example, `sensor-001` and open both URLs. Also test an unknown ID such as `sensor-999` and check that the error handling is clear.

```text
http://localhost:5001/devices/sensor-001/measurements
http://localhost:5001/devices/sensor-001/latest
```

Run the tests inside the API container:

```text
docker compose exec api python -m pytest -q
```

**Common pitfall:** JSON uses `deviceId`, while the database column is called `device_id`. Translate the name consistently in one place in the code.

### 4. Basic SQL Tasks

**Mandatory:** Write and test three SQL queries that show:

* total number of measurements using `COUNT`
* average temperature using `AVG`
* measurements from the last 24 hours

The following shows the syntax. Adapt the table, column, and conditions to the lab's schema:

```sql
SELECT COUNT(*) FROM table_name;
SELECT AVG(column_name) FROM table_name;
SELECT * FROM table_name
WHERE timestamp_column >= NOW() - INTERVAL '24 hours';
```

Open the PostgreSQL client with:

```text
docker compose exec db psql -U student -d jensen_iot
```

**Check:** Each query should run without errors and produce a reasonable result based on the data in the `measurements` table. Exit `psql` with:

```text
\q
```

Save the three queries and a short explanation in the project's README or in a separate `.sql` file linked from the README.

**Optional extension:** Implement `/statistics`, Online/Offline status, or calculations for the highest average temperature and most active sensor.

# Milestone 2 – Docker and Cache

### 1. Understand the Environment and Verify Persistence

**Mandatory:** Read `docker-compose.yml` and identify the services `api`, `simulator`, `db`, and `redis`, their dependencies, and which ports are exposed to your computer.

**Persistence check:**

1. Note a measurement using `/measurements`.
2. Run `docker compose down` and then `docker compose up -d`.
3. Check that the measurement is still present. PostgreSQL uses the `postgres_data` volume.

### 2. Implement Latest-Measurement Cache

**Mandatory:**

1. Implement `get_latest_from_cache` and `set_latest_in_cache` in `api/cache.py`.
2. Use a clear key format, for example `latest:sensor-001`, and JSON for the value.
3. In the latest-measurement endpoint, read from Redis first. On a cache miss, read from PostgreSQL and store the result in the cache before returning it.
4. Update the cache when a new measurement is saved.
5. PostgreSQL should continue to be the persistent source of truth; an empty Redis must not cause the history to disappear.

Redis stores text. Use `json.dumps(measurement)` when storing the value and `json.loads(value)` when reading it. The `json` import and Redis client are already prepared in `api/cache.py`.

**Check:** Retrieve the latest value and then check the keys:

```text
docker compose exec redis redis-cli KEYS "latest:*"
```

You should see at least one key after a latest measurement has been retrieved or stored.

Test a cache miss by clearing only the lab's Redis:

```text
docker compose exec redis redis-cli FLUSHDB
```

The next GET request should still be able to read the value from PostgreSQL and recreate the cache entry.

**Reflection:** Explain why history is stored in PostgreSQL while the latest measurement is suitable for caching, and what happens if either service becomes unavailable.

# Milestone 3 – CI and Introduction to Kubernetes

### 1. Create a Simple CI Pipeline

**Mandatory:** Create `.github/workflows/ci.yml` in your fork. The pipeline should run on push and pull request and must at least:

1. check out the code
2. install `api/requirements.txt`
3. run the tests using `python -m pytest`
4. build the API Docker image

This is a minimal example that you can use and explain:

```yaml
name: CI
on: [push, pull_request]

jobs:
  test-and-build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - run: pip install -r api/requirements.txt
      - run: python -m pytest tests -q
        working-directory: api
      - run: docker build -t jensen-iot-api:ci ./api
```

The same tests can be run locally with:

```text
docker compose exec api python -m pytest -q
```

Push the workflow file and check the **Actions** tab on GitHub. A green run is the expected verification.

### 2. Start Minikube and Build the Image

The Kubernetes section is a short, introductory but practical demonstration of the API. You should start Minikube, deploy the API, and complete the steps below.

PostgreSQL, Redis, and the simulator should **not** be deployed in Kubernetes. Therefore, the start page and `/health` are used in this exercise; database-dependent endpoints are not included in this demo.

**Mandatory:** First check that Docker, Minikube, and `kubectl` work. Then start the cluster and build the image directly in Minikube:

```text
minikube start --driver=docker
minikube status
minikube image build -t jensen-iot-api:lab ./api
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl get pods
```

Wait until all three Pod statuses are `Running` and `READY` shows `1/1`.

If there are problems, use:

```text
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

### 3. Access the Service

**Mandatory:** Run the following and keep the terminal window open while testing the page:

```text
minikube service jensen-iot-api
```

Minikube will display or open the correct URL. Do not assume `localhost:30080`; the address varies depending on the operating system and Minikube driver.

If the browser does not open automatically, you can get the address with:

```text
minikube service jensen-iot-api --url
```

As a temporary alternative, you can run:

```text
kubectl port-forward service/jensen-iot-api 8080:80
```

Then open:

```text
http://localhost:8080
```

Stop an active service tunnel or port-forward with `Ctrl+C`.

### 4. Observe Self-Healing and Scaling

**Mandatory:**

1. Run `kubectl get pods` and copy the name of one Pod.
2. Delete that Pod:

```text
kubectl delete pod <pod-name>
```

3. Run:

```text
kubectl get pods -w
```

The Deployment should create a replacement so that the number of Pods returns to three. Stop monitoring with `Ctrl+C`.

4. Scale up and then back down:

```text
kubectl scale deployment jensen-iot-api --replicas=5
kubectl get pods
kubectl scale deployment jensen-iot-api --replicas=3
kubectl get pods
```

This demonstrates **self-healing** and **scaling**.

The manifest contains a Deployment that manages Pod replicas and a Service that provides a common entry point for them.

Advanced cluster configuration, rolling updates, and rollback are **not mandatory**.

When the exercise is complete:

```text
minikube stop
```

**Reflection:** Describe what you observed when a Pod was deleted, why multiple replicas can increase availability, and when Kubernetes would be unnecessarily large for a solution.

# Milestone 4 – Documentation and Submission Files

**Mandatory:** Complete the following in your fork:

* Project `README.md`: describe your solution, prerequisites, how it is built/started, how tests are run, and any known limitations. Adapt the starter text so that it reflects your final result.
* `docs/architecture.md` and a linked/exported diagram according to the instructions in the file.
* `docs/reflection.md`: provide short but well-motivated answers to all questions.
* Code and tests, together with a green CI run.

The submission format, access, deadline, and formal assessment criteria are provided in the assignment document.

# Connection to Assessment Criteria

| Requirement in Assignment                    | Practical Support in Repository          |
| -------------------------------------------- | ---------------------------------------- |
| Working REST API                             | Milestone 1, endpoints, and verification |
| API, PostgreSQL, and Docker Compose          | Milestones 1–2 and `docker-compose.yml`  |
| Validation and HTTP status codes             | Milestone 1, `validation.py`, and tests  |
| SQL tasks                                    | Milestone 1, section 4                   |
| Kubernetes: scaling and self-healing         | Milestone 3, sections 2–4                |
| Cache and CI                                 | Milestones 2–3                           |
| README, architecture diagram, and reflection | Milestone 4 and templates in `docs/`     |

The tasks marked **Mandatory** support the requirements for passing the assignment.

The three basic SQL queries (`COUNT`, `AVG`, and the last 24 hours) are mandatory. More advanced analysis and `/statistics` are optional extensions.

# Final Submission Checklist

Before submitting, check that:

* `docker compose up --build -d` starts all four services without errors
* the simulator receives `201` for valid measurements and `400` for invalid measurements
* all mandatory endpoints return the expected JSON and status codes
* measurements remain available after `docker compose down` and a new start
* the latest measurement can be restored from PostgreSQL after Redis is cleared
* `docker compose exec api python -m pytest -q` passes
* the latest CI run on GitHub is green
* the Kubernetes demonstration has been completed with three replicas, self-healing, and scaling
* the README, architecture diagram, and reflection document are complete and pushed
