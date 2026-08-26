Here is the English version:

1. **Why should the sensors communicate with an API instead of directly with PostgreSQL?**
2. **Why should invalid sensor data be stopped before it is saved?**
3. **Why is PostgreSQL suitable for historical measurement data?**
4. **What happens to the solution if Redis disappears?**
5. **What happens to the solution if PostgreSQL disappears?**
6. **Why is Docker Compose used locally?**
7. **What does your CI pipeline automate?**
8. **What did you observe when you removed a Kubernetes Pod?**
9. **Why can multiple replicas provide higher availability?**
10. **When would Kubernetes be overkill for a solution?**

**Instruction:** Answer all questions briefly but with justification. Connect your answers to what you implemented and observed during the lab. Simple yes/no answers are not sufficient. Replace the instruction text with your answers before submission.

Save the answers in this file. The architecture diagram is submitted separately according to `docs/architecture.md`.
(https://github.com/bastosnov/jensen-IOT-lab/)
Absolutely. Here are **short but motivated answers in English**, written so you can use them directly in your lab submission. They are connected to the IoT solution with **REST API, PostgreSQL, Redis, Docker Compose, CI, and Kubernetes**.

# Lab Questions – Answers

### 1. Why should the sensors communicate with an API instead of directly with PostgreSQL?

The sensors communicate with the REST API because the API provides validation, security, and a clear interface between the sensors and the database. In the lab, the API validated the sensor data before storing it in PostgreSQL. This also means that the sensors do not need direct database access.

### 2. Why should invalid sensor data be stopped before it is saved?

Invalid data should be stopped before saving because incorrect values would make the historical data unreliable. In the lab, validation helped ensure that temperature, humidity, and battery values were within the expected ranges. This makes the stored data more trustworthy.

### 3. Why is PostgreSQL suitable for historical measurement data?

PostgreSQL is suitable because it is a relational database that can store structured sensor measurements reliably over time. In the lab, measurements were stored as historical records, which makes it possible to query and analyse previous sensor values.

### 4. What happens to the solution if Redis disappears?

If Redis disappears, the system can still use PostgreSQL as the source of historical data, but requests for the latest measurement will be slower because the cache is no longer available. Redis improves performance by providing quick access to frequently requested data.

### 5. What happens to the solution if PostgreSQL disappears?

If PostgreSQL disappears, the application loses its persistent storage. Sensor data can no longer be stored as historical records. Redis may still contain some cached data temporarily, but it cannot replace PostgreSQL as the permanent database.

### 6. Why is Docker Compose used locally?

Docker Compose is used to start and manage several services together, such as the REST API, PostgreSQL, and Redis. In the lab, this made it easier to create the same local environment repeatedly without installing and configuring every service manually.

### 7. What does your CI pipeline automate?

The CI pipeline automatically checks the project when changes are pushed to GitHub. It can install dependencies, run tests, and verify that the application builds correctly. This helps detect errors early before changes are considered ready.

### 8. What did you observe when you removed a Kubernetes Pod?

When a Kubernetes Pod was removed, Kubernetes automatically created a new Pod because the deployment was configured to maintain the desired number of replicas. This demonstrated Kubernetes' self-healing behaviour.

### 9. Why can multiple replicas provide higher availability?

Multiple replicas mean that several instances of the application are running at the same time. If one Pod fails, another replica can continue serving requests. Therefore, the application can remain available even when one instance goes down.

### 10. When would Kubernetes be overkill for a solution?

Kubernetes can be overkill for a small application with only a few services, low traffic, and simple deployment requirements. For example, the IoT lab can run effectively with Docker Compose locally. Kubernetes becomes more useful when the system needs scaling, high availability, automatic recovery, and management of many containers.
