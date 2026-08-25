# Cloud Developer – Individual Exercise

## Goal

* Start databases using Docker
* Connect to databases locally
* Understand the difference between SQL and NoSQL
* Implement CRUD
* Discuss trade-offs and scaling

## Scenario

You are working as cloud developers at **NovaStore**, a growing e-commerce platform in the Nordic region.

The system is starting to experience problems:

* Slow queries
* Duplicate users
* Negative inventory
* Difficult-to-manage product data
* Increasing traffic

The team now needs to improve the data layer.

You need to:

* Set up databases using Docker
* Connect to the databases
* Implement CRUD operations
* Model data using SQL and NoSQL
* Troubleshoot bugs and bottlenecks
* Discuss which types of data are best suited for SQL versus NoSQL

---

# Part 1 — Setup with Docker

## `docker-compose.yml`

```yaml
services:
  postgres:
    image: postgres:16
    container_name: sql-demo
    environment:
      POSTGRES_USER: student
      POSTGRES_PASSWORD: student
      POSTGRES_DB: shop
    ports:
      - "5432:5432"

  mongo:
    image: mongo:7
    container_name: nosql-demo
    ports:
      - "27017:27017"
```

---

# Part 2 — Start the Databases

```bash
docker compose up -d
```

Check the running containers:

```bash
docker ps
```

## Connect to PostgreSQL

```bash
docker exec -it sql-demo psql -U student -d shop
```

Test the connection:

```sql
SELECT NOW();
```

## Connect to MongoDB

Via terminal:

```bash
docker exec -it nosql-demo mongosh
```

Show databases:

```javascript
show dbs
```

MongoDB connection string:

```text
mongodb://localhost:27017
```

---

# Part 3 — SQL: PostgreSQL

Create the `customers` table:

```sql
CREATE TABLE customers (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT UNIQUE NOT NULL
);
```

Create the `products` table:

```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    price DECIMAL NOT NULL,
    category TEXT,
    stock INT DEFAULT 0
);
```

Create the `orders` table:

```sql
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    customer_id INT REFERENCES customers(id),
    status TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);
```

Create the `order_items` table:

```sql
CREATE TABLE order_items (
    id SERIAL PRIMARY KEY,
    order_id INT REFERENCES orders(id),
    product_id INT REFERENCES products(id),
    quantity INT NOT NULL
);
```

## CRUD Tasks

* Create customers and products
* Create orders
* Retrieve data using `JOIN`
* Update the order status
* Delete products

## JOIN Example

```sql
SELECT customers.name, orders.id, orders.status
FROM orders
JOIN customers ON orders.customer_id = customers.id;
```

## Index

```sql
CREATE INDEX idx_customers_email
ON customers(email);
```

---

# Part 4 — NoSQL: MongoDB

Example order document:

```json
{
  "order_id": "order_1",
  "customer": {
    "id": "customer_1",
    "name": "Sara",
    "email": "sara@example.com"
  },
  "items": [
    {
      "product_id": "product_1",
      "name": "Laptop",
      "price": 12990,
      "quantity": 1
    }
  ],
  "status": "created"
}
```

## CRUD Tasks

* Create documents
* Retrieve orders
* Update the status
* Delete documents
* Implement soft delete

---

# Part 5 — Bugs and Bottlenecks

## Problem A: Slow Query

```sql
SELECT *
FROM customers
WHERE LOWER(email) = LOWER('sara@example.com');
```

Discuss:

* Why does this query become slow?
* How can it be improved?

---

## Problem B: Duplicate Data in MongoDB

```javascript
db.customers.insertMany([
  { name: "Sara", email: "sara@example.com" },
  { name: "Sara Backup", email: "sara@example.com" }
])
```

Discuss:

* Why are duplicate customers allowed?
* How can you prevent duplicate email addresses in MongoDB?

---

## Problem C: Negative Inventory

```sql
UPDATE products
SET stock = stock - 1
WHERE id = 1;
```

Two users purchase the last product at the same time.

Discuss:

* What happens?
* How can you prevent the stock from becoming negative?
* How can transactions or conditional updates help?

---

# Part 6 — Connect via Code

## PostgreSQL in Python

### Install the package

```bash
pip install psycopg2-binary
```

### Python code

```python
import psycopg2

conn = psycopg2.connect(
    host="localhost",
    port=5432,
    user="student",
    password="student",
    database="shop"
)

cursor = conn.cursor()

cursor.execute("SELECT * FROM customers")

rows = cursor.fetchall()

for row in rows:
    print(row)

cursor.close()
conn.close()
```

---

## MongoDB in Python

### Install the package

```bash
pip install pymongo
```

### Connect to MongoDB

```python
from pymongo import MongoClient

client = MongoClient("mongodb://localhost:27017")

db = client["shop"]

print("Connected!")

collections = db.list_collection_names()

print(collections)
```

### Retrieve orders

```python
from pymongo import MongoClient

client = MongoClient("mongodb://localhost:27017")

db = client["shop"]

orders = db.orders.find()

for order in orders:
    print(order)
```

---

# Part 7 — Discussion

Discuss the following questions:

* When is SQL the best choice?
* When is NoSQL the best choice?
* What data must be correct immediately?
* What data can use eventual consistency?
* How would you scale the system?

---

# Part 8 — Stretch Goals

* Implement pagination
* Add Redis using Docker
* Implement transactions
* Add Docker volumes
* Create an architecture diagram using draw.io

---

# Presentation on Friday

You should present:

* SQL model
* NoSQL model
* CRUD examples
* Trade-offs

