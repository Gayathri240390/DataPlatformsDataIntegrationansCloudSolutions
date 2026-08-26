
# Lab: Data Platforms, Data Integration and Cloud Solutions

## Overview

This lab introduces students to the design and implementation of a cloud-based IoT data platform. The purpose is to build a small, realistic system that collects sensor data, validates it, stores it in a database, caches recent values for faster access, and exposes the information through a REST API.

The project combines practical software development with cloud concepts such as containerization, data flow, integration, and scalable architecture.

---

## Lab Objective

By the end of this lab, students should be able to:

- understand how IoT data moves from sensors to storage and retrieval
- implement API endpoints and validation logic
- work with PostgreSQL and Redis in a real application
- run services with Docker Compose
- deploy a basic application in Kubernetes
- explain the architecture choices behind cloud data systems

---

## Scenario

A company uses several connected sensors to monitor environmental conditions such as temperature and humidity. These devices send readings to a backend system, which must:

- validate incoming data
- accept only correct measurements
- store valid readings in a database
- provide recent data through a fast cache
- make the information available to other services or clients

This scenario reflects a real-world IoT architecture used in smart environments, industrial systems, and connected devices.

---

## Tools and environment

Before starting, ensure the following tools are installed:

- Git and a GitHub account
- Docker Desktop or Docker Engine with Docker Compose
- VS Code or another code editor
- kubectl and Minikube for the Kubernetes part of the assignment

Check the installation:

```bash
git --version
docker --version
docker compose version
kubectl version --client
minikube version
```

> Python does not need to be installed locally for the basic lab tasks because the project runs inside containers.

---

## Project structure

The repository contains the core building blocks of the lab:

- `api/` — backend logic, validation, database access, cache access, and tests
- `simulator/` — simulated IoT sensors sending readings to the API
- `database/` — SQL schema and initial database data
- `k8s/` — Kubernetes manifests for deployment
- `docs/` — instructions, architecture guidance, and reflection templates

The main services are:

- REST API
- PostgreSQL database
- Redis cache
- Sensor simulator
- Kubernetes deployment files

---

## Step 1: Clone and start the environment

### 1. Clone the repository

```bash
git clone <URL-OF-YOUR-FORK>
cd <REPOSITORY-FOLDER>
```

Run all Docker Compose commands from the repository root, where `docker-compose.yml` is located.

### 2. Start the application containers

```bash
docker compose up --build -d
docker compose ps
```

The expected containers are:

- `api`
- `simulator`
- `db`
- `redis`

### 3. Check the API

Open the following endpoints:

- `http://localhost:5001`
- `http://localhost:5001/health`
- `http://localhost:5001/devices`
- `http://localhost:5001/measurements`

The measurement endpoint may initially return an empty list. This is expected while data persistence is still being implemented.

### 4. Observe the simulator output

```bash
docker compose logs -f simulator
```

Valid readings should be accepted by the API, while intentionally invalid sensor data should receive a `400` response. This is part of the lab challenge.

---

## Step 2: Milestone-based development

The lab is divided into milestones that follow the data flow of a realistic IoT system.

### Milestone 1: API and validation

Focus on:

- correct handling of sensor requests
- validating required fields and data values
- returning the proper HTTP status codes
- handling invalid inputs safely

Main files:

- `api/app.py`
- `api/validation.py`

Tasks:

- accept valid sensor measurements
- reject bad payloads with a clear response
- ensure the API behaves correctly under normal and erroneous conditions

### Milestone 2: Database persistence

Focus on:

- storing measurements in PostgreSQL
- implementing database queries for insert and read operations
- retrieving device data and measurement history

Main files:

- `api/db.py`
- `database/init.sql`

Tasks:

- save valid measurements into the database
- test CRUD-style behavior in the backend
- confirm the stored data is returned correctly

### Milestone 3: Caching with Redis

Focus on:

- improving performance using a cache
- storing recent values for fast access
- reducing repeated database reads

Main file:

- `api/cache.py`

Tasks:

- cache recent sensor values
- implement a fallback strategy when data is missing from the cache
- ensure the application remains consistent and responsive

### Milestone 4: Kubernetes deployment

Focus on:

- understanding orchestration and deployment
- applying Kubernetes manifests
- comparing local Docker Compose deployment with cluster deployment

Main folder:

- `k8s/`

Tasks:

- deploy the application in a local Kubernetes environment
- verify that pods and services are running correctly
- explain why orchestration is useful in larger systems

---

## Testing the solution

After modifying the code, rebuild the containers and run the automated tests:

```bash
docker compose up --build -d
docker compose exec api python -m pytest -q
```

If something goes wrong, inspect the application logs:

```bash
docker compose logs --tail=100 api
```

---

## Troubleshooting

If startup fails:

- check that Docker is running
- confirm you are in the repository root
- verify there is no port conflict
- inspect the health of each service with:

```bash
docker compose ps
docker compose logs api db redis simulator
```

If port `5001` is already in use, run the project on another port:

### Windows PowerShell

```powershell
$env:API_PORT=5002; docker compose up --build
```

### macOS/Linux

```bash
API_PORT=5002 docker compose up --build
```

---

## Expected learning outcomes

This lab helps students understand the connection between:

- device data generation
- API communication
- data validation
- relational storage
- caching for performance
- cloud deployment and orchestration

The main idea is that a successful cloud platform is not only about code; it depends on architecture, reliability, and scalability.

---

## Reflection questions

Students should be able to answer questions such as:

- Why is validation important before storing measurements?
- Why is PostgreSQL useful for structured sensor data?
- What advantages does Redis provide in a data flow pipeline?
- Why use Docker Compose for local development and Kubernetes for deployment?
- What are the trade-offs between performance, cost, and reliability?

---

## Assessment context

This laboratory is part of the course assessment and must be completed to become eligible for the written exam. The written exam includes questions based on the tasks and concepts used in the lab.

The course expects students not only to implement the solution but also to explain the technology choices and justify the architecture.

---

## Final note

This lab is designed to give students practical experience with a modern cloud-based data system. It is important to complete the milestones in order, test frequently, and document design decisions carefully.

A strong submission shows both technical implementation and a clear understanding of how cloud platform components work together.

---

## Quick command summary

```bash
docker compose up --build -d
docker compose ps
docker compose logs -f simulator
docker compose exec api python -m pytest -q
docker compose down
```

Use the following only when you want to delete local database data:

```bash
docker compose down -v
```


