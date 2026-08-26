# Complete Project Structure & Python Files Setup Guide

This document outlines all the required Python files, configuration files, and folder structure needed to build the **Jensen IoT Data Platform**.

---

## 📁 Required Directory Structure

To build the complete solution, create the following folders and files in your repository root:

```text
├── api/
│   ├── app.py                  # Main REST API application (Flask)
│   ├── validation.py           # Data validation rules
│   ├── db.py                   # PostgreSQL queries & connections
│   ├── cache.py                # Redis caching methods
│   ├── requirements.txt        # Python dependencies for API
│   ├── Dockerfile              # Docker image definition for API
│   └── tests/
│       └── test_validation.py  # Pytest suite for validation
├── simulator/
│   ├── simulator.py            # Simulated IoT sensors script
│   ├── requirements.txt        # Dependencies for simulator
│   └── Dockerfile              # Docker image definition for simulator
├── database/
│   └── init.sql                # PostgreSQL table schema and initial devices
├── k8s/
│   ├── deployment.yaml         # Kubernetes deployment manifest
│   └── service.yaml            # Kubernetes service manifest
├── docs/
│   ├── architecture.md         # Architecture diagram description
│   └── reflection.md           # Answers to 10 reflection questions
├── .github/
│   └── workflows/
│       └── ci.yml              # GitHub Actions CI workflow
├── docker-compose.yml          # Local multi-container Docker config
└── README.md                   # Project documentation & SQL tasks
```

---

## 🐍 Python (`.py`) Files & Implementation Details

### 1. `api/validation.py`
**Purpose:** Validates incoming payload JSON objects from sensors before saving them to the database.

**Key Functions:**
- `validate_measurement(data)`:
  - Checks for required fields: `deviceId`, `temperature`, `humidity`, `battery`.
  - Ensures data types are numbers (`int` or `float`).
  - Ensures values are within realistic sensor ranges:
    - `temperature`: -50 to 100
    - `humidity`: 0 to 100
    - `battery`: 0 to 100
  - Returns `(is_valid: bool, error_message: str)`.

---

### 2. `api/db.py`
**Purpose:** Handles all SQL queries and database interactions with PostgreSQL.

**Key Functions:**
- `get_db_connection()`: Connects to PostgreSQL using environment variables (`POSTGRES_HOST`, `POSTGRES_DB`, etc.).
- `device_exists(device_id)`: Executes `SELECT 1 FROM devices WHERE id = %s` to check if a sensor is registered.
- `insert_measurement(data)`: Executes `INSERT INTO measurements (device_id, temperature, humidity, battery) VALUES (%s, %s, %s, %s) RETURNING *;`.
- `get_measurements_for_device(device_id)`: Retrieves history of measurements for a specific sensor ordered by timestamp descending.
- `get_latest_measurement(device_id)`: Retrieves the single most recent measurement for a sensor.

---

### 3. `api/cache.py`
**Purpose:** Manages key-value caching of the latest sensor measurements in Redis.

**Key Functions:**
- `get_redis_client()`: Connects to Redis host.
- `set_latest_in_cache(device_id, measurement)`:
  - Key format: `latest:<device_id>` (e.g., `latest:sensor-001`).
  - Converts dictionary to JSON string via `json.dumps(measurement)`.
- `get_latest_from_cache(device_id)`:
  - Reads key from Redis.
  - Converts JSON string back to dictionary via `json.loads(data)`.

---

### 4. `api/app.py`
**Purpose:** Main Flask REST API application exposing HTTP endpoints.

**Key Endpoints:**
- `POST /measurements`:
  1. Calls `validate_measurement(data)`.
  2. Calls `device_exists(data['deviceId'])`. Returns `400 Bad Request` if unknown device or invalid payload.
  3. Calls `insert_measurement(data)` to save to PostgreSQL.
  4. Updates Redis cache via `set_latest_in_cache`.
  5. Returns `201 Created`.
- `GET /measurements`: Returns all recent measurements.
- `GET /devices`: Returns list of all devices in the database.
- `GET /devices/<device_id>/measurements`: Returns device measurement history (`200 OK` or `404 Not Found`).
- `GET /devices/<device_id>/latest`:
  1. Tries `get_latest_from_cache(device_id)` first.
  2. If cache miss, calls `get_latest_measurement(device_id)` from PostgreSQL DB.
  3. Updates cache and returns measurement (`200 OK` or `404 Not Found`).

---

### 5. `api/tests/test_validation.py`
**Purpose:** Automated unit tests using `pytest`.

**Required Test Cases:**
- Valid payload returns True.
- Missing `deviceId` returns False.
- Missing `temperature` returns False.
- Temperature out of range returns False.
- Invalid data type for `humidity` returns False.
- Invalid data type for `battery` returns False.

Run tests in container:
```bash
docker compose exec api python -m pytest -q
```

---

### 6. `simulator/simulator.py`
**Purpose:** Simulates IoT hardware devices sending periodic HTTP POST requests.

**Key Logic:**
- Simulates 3 devices (`sensor-001`, `sensor-002`, `sensor-003`).
- Sends valid measurement JSON payloads to `http://api:5001/measurements`.
- Intentionally sends periodic invalid payloads to test that the API responds with HTTP `400`.

---

## 🛠️ Docker & Configuration Setup

### `api/requirements.txt`
```text
Flask==3.0.3
psycopg2-binary==2.9.9
redis==5.0.4
pytest==8.2.0
```

### `docker-compose.yml` Overview
Configures 4 linked container services:
1. `api`: Flask REST API running on port `5001`.
2. `db`: PostgreSQL database running on port `5432` with volume `postgres_data`.
3. `redis`: Redis cache server running on port `6379`.
4. `simulator`: Python simulator service.

---

## 🚀 Execution & Verification Commands

1. **Start all services**:
   ```bash
   docker compose up --build -d
   ```
2. **Check container logs**:
   ```bash
   docker compose logs -f api simulator
   ```
3. **Run unit tests**:
   ```bash
   docker compose exec api python -m pytest -q
   ```
4. **Connect to PostgreSQL**:
   ```bash
   docker compose exec db psql -U student -d jensen_iot
   ```
5. **Inspect Redis Cache**:
   ```bash
   docker compose exec redis redis-cli KEYS "latest:*"
   ```
