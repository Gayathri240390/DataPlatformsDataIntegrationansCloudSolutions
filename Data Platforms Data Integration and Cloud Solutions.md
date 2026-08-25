# Data Platforms, Data Integration and Cloud Solutions

**2026-05-20 – COURSE START**

## Agenda – Course Start

| TimeTopic   |                                   |
| ----------- | --------------------------------- |
| 09:00–10:00 | Introduction to Cloud             |
| 10:00–11:00 | Data Types & Data Storage         |
| 11:00–12:00 | How is Data Used in Real Systems? |
| 12:00–13:00 | Lunch                             |
| 13:00–14:00 | Security & Access Control         |
| 14:00–15:00 | Cost & Trade-offs                 |
| 15:00–16:00 | Exercise and Deep Dive            |

---

# Introduction to Cloud

### Question 1 / 3 / Everyone

**What do you think of when you hear the word "Cloud"?**

## What is Cloud?

- **Cloud computing:** Delivering computing resources over the internet on demand.
- **Cloud is just someone else's computer.**

### Simply explained

- IT resources over the internet
- On-demand
- Pay for what you use
- No need to own physical hardware

### On-premises vs Cloud

**On-premises:**

- Buy servers
- Manual scaling
- CAPEX
- Takes weeks/months

**Cloud:**

- Rent resources
- Automatic scaling
- OPEX
- Takes minutes

### On-Premises vs Cloud

**On-premises:**

- Full control
- May be required in large industries
- Local latency

**Cloud:**

- Fast provisioning
- Pay only for what you use
- Global infrastructure

### Question

**What happens if a company suddenly gets 10× more users?**

---

# Why is Cloud Used?

- Scalability
- Speed
- Lower initial cost
- Global availability
- Innovation

### Examples of B2C Companies

- Netflix
- Spotify
- Uber
- SKF

---

# The Value Behind Cloud

## Example from the Industry – Netflix

### On-premises

1. Buy servers
2. Wait weeks/months
3. Rack the hardware
4. Install the network
5. Configure the operating system

### Cloud

1. Click / IaC (Infrastructure as Code)
2. Server is running within minutes

---

# Scalability

There are two main ways to scale:

### Vertical Scaling

Use a **larger and more powerful server**:

- More RAM
- More CPU

### Horizontal Scaling

Use **more servers** that can handle more requests.

---

# Scalability vs Elasticity

### Scalability

Means that a service **can grow** when needed.

### Elasticity

Means that the system **automatically grows or shrinks depending on the workload**.

### Example

The system automatically scales according to the amount of traffic.

### Question

**How do you think Netflix's traffic usage in Sweden looks throughout the day?**

---

# High Availability

High availability means:

> The system continues to work even if parts of the system fail.

### What does it answer?

**What happens if one server goes down?**

### Example

If a server crashes:

- Traffic can be sent to another server.
- Multiple servers are used.
- A load balancer distributes traffic.
- Multiple data centers can be used.

### Another Question

**What happens if we suddenly get a large number of users from Japan?**

---

# Shared Responsibility Model

Cloud security is a shared responsibility between the **cloud provider** and the **customer**.

### Cloud Provider

Responsible for:

- Data center
- Hardware
- Network
- Infrastructure

### Customer

Responsible for:

- Data
- IAM
- Configuration
- Permissions

### Question

**If a Blob Storage becomes public, whose fault is it?**

Usually, the customer is responsible if the storage was incorrectly configured.

---

# Cloud Providers

Large companies manage server environments and offer cloud services under their own names.

Examples include:

- AWS
- Azure
- Google Cloud

> The concepts are more important than the names of the individual services.

### Question 1 / 3 / Everyone

**Why might a company NOT want to move to the cloud?**

---

# Data Types & Data Storage

### Question

**What is storage, really?**

## Storage

All storage is basically about:

> **Reading and writing data.**

---

# Is All Data the Same?

There are three main types of data:

### 1. Structured Data

Highly organized data, usually stored in databases.

Example:

- Customer name
- Email
- Order number
- Product ID

### 2. Semi-Structured Data

Data that has some structure but is more flexible.

Example:

- JSON
- XML

### 3. Unstructured Data

Data without a fixed structure.

Examples:

- Images
- Videos
- PDFs
- Binary files

---

# Three Types of Storage

1. **Block Storage**
2. **File Storage**
3. **Object Storage**

---

# Block Storage

Examples:

- SSD
- NVMe
- AWS EBS

### Advantages

- Low latency
- High IOPS
- Direct access
- The operating system can control the filesystem and caching

### Commonly Used For

- Databases
- Operating systems

### Weaknesses

- Not suitable for massive media storage
- Not ideal for sharing files between many systems
- Expensive for long-term storage

---

# File Storage

Examples:

- NFS
- SMB
- AWS EFS

### Advantages

- Shared files
- Web servers
- Home directories

### Commonly Used For

Shared files that can be accessed by the whole organization.

### Weaknesses

- Limited extreme scalability
- Not ideal for Big Data
- Lower performance at very large scale

---

# Object Storage

Examples:

- AWS S3
- Azure Blob Storage

### Common Uses

- Images
- Videos
- Backups
- Logs
- AI datasets
- Static websites

### Advantages

Object storage is extremely scalable and can store **billions of objects**.

It is generally:

- Cheap
- Highly durable
- Rich in metadata
- Good for searching and analysis

### Weaknesses

- Higher latency
- Less suitable for many small writes
- You cannot efficiently edit the middle of a file

---

# Block vs File vs Object

Think of them like this:

### Block Storage

> **"Give me a raw disk."**

### File Storage

> **"Give me a filesystem."**

### Object Storage

> **"Store this object."**

---

# Storage Exercise

### Which storage type would you choose for:

- A Netflix service?
- A hospital medical-record system?

---

# Scenario: Social Media App

A social media application has:

### Profile Information

- Name
- Email
- Password

This is **structured data** that needs to be stored in a database.

### Content

- Images
- Videos

This is **unstructured data** → **Object Storage**

### Shared Files for Administrators

→ **File Storage**

### Important Point

A system often uses **multiple types of storage at the same time**.

---

# How Data Is Used in Real Systems

## Data Temperature

Not all data needs the same performance.

### Hot Data

Data that is used frequently.

Examples:

- Active database
- User sessions
- Dashboards

Characteristics:

- Fast access
- Low latency
- More expensive storage

### Cold Data

Data that is rarely used.

Examples:

- Backups
- Logs
- Historical data

Characteristics:

- Cheap storage
- High durability
- Higher latency

---

# Data Lifecycle

Cloud is about:

- Automation
- Cost optimization
- Data lifecycle management

Data can move through different storage levels over time:

**Day 1 → Day 30 → Year 1 → Year 7**

For example:

**Fast storage → Cheaper storage → Archive → Delete**

### Question

**Does all old data need to be immediately accessible?**

---

# Backup vs Archive

### Backup

Designed for:

- Recovery
- Fast restoration
- Shorter retention

### Archive

Designed for:

- Long-term storage
- Lower cost
- Long retention
- Compliance/history

### Question

**Why can't we use backup as an archive?**

Because backups are primarily designed for **recovery**, while archives are designed for **long-term retention at lower cost**.

---

# Metadata

Have you ever wondered how Netflix finds the right video among billions of files?

Metadata can include:

- File size
- Creation date
- Content type
- Tags
- Owner

### Metadata is used for:

- Searching
- Analysis
- Automation
- AI
- Filtering

---

# Replication

Replication means:

> **Data is copied to multiple physical locations.**

### Why?

- Higher availability
- Redundancy
- Disaster recovery

### Important

If data is deleted, replication may also replicate the deletion.

**Replication alone does NOT protect you from accidental deletion or corrupted data.**

---

# Durability vs Availability

### Durability

**Does the data still exist?**

Example:

If your computer breaks, your files can still exist in Google Drive.

### Availability

**Can I use the system right now?**

Example:

If a server crashes while you are making an online banking transaction, your traffic can be sent to another server.

---

## Example 1

The system is unavailable for 5 minutes, but no data is lost.

→ **Poor Availability**
→ **Good Durability**

## Example 2

The system is working, but the database becomes corrupted and all data is lost.

→ **Good Availability**
→ **Poor Durability**

---

# How the Industry Thinks

When designing storage, companies consider:

### Cost

Should we use fast storage or cheap storage?

### Availability

Can the system always be accessed?

### Security

How much security is required?

### Performance

Do all services need to be equally fast?

### Question

**If you build a new system, which two or three of these would you prioritize most?**

- Cost
- Availability
- Performance
- Security

---

# Security & Reliability

### Question

**What is the worst thing that can happen to a system?**

---

# CIA Triad

The CIA Triad is a fundamental security concept.

### Confidentiality

**Who is allowed to read the data?**

### Integrity

**Has the data been changed or corrupted?**

### Availability

**Can the system/service be used when needed?**

---

# Encryption

### Question

**Why isn't it enough to encrypt the database?**

Data needs protection in different situations.

### At Rest

Data stored on:

- Disk
- Database
- Storage

Can be protected using **disk/database encryption**.

### In Transit

Data being transferred over a network.

Can be protected using:

- HTTPS
- TLS

---

# IAM & Least Privilege

### Bad approach

Everyone is an administrator and can do whatever they want.

**Simple, but dangerous.**

### Better approach

Create different roles:

- Developer
- Administrator
- Support
- User

### Principle of Least Privilege

> **Never give someone more access than they need.**

---

# Public vs Private Data

## Public Data

Examples:

- Public images
- Static websites
- Documentation

## Private Data

Examples:

- Profile pictures
- Company party photos
- Customer data
- Banking information

### Important

Many cloud security breaches happen because **storage is incorrectly configured and accidentally made public**.

---

# Backup

Backup means:

> **A copy of data that can be used to restore a system after a problem or disaster.**

### Why is backup needed?

Examples:

- Someone accidentally deletes the database.
- An important file is deleted.
- A developer runs the wrong script.

Backup allows the system/data to be restored.

---

# Replication

Replication means:

> **Data is copied to multiple systems or regions.**

### Why?

- High availability
- Redundancy
- Disaster recovery

### Important

Replication **does not protect against accidental deletion or corrupted data** because the deletion/corruption may also be replicated.

---

# Disaster Recovery

Imagine that everything goes wrong:

- Data center fire
- Cyberattack
- Entire cloud region goes down

The question isn't:

> **"Do we need to recover?"**

The question is:

> **"How quickly do we need to recover?"**

---

# Recovery Time Objective – RTO

RTO answers:

> **How long can the system be down?**

### Example

How long can Snapchat be unavailable before users become angry?

- 1 minute?
- 5 minutes?
- 30 minutes?

That required recovery time is the **RTO**.

---

# Recovery Point Objective – RPO

RPO answers:

> **How much data can we afford to lose?**

### Example

If the latest 1 minute of Snapchat messages disappear, is that acceptable?

- 5 minutes?
- 30 minutes?

If the system can only be restored to yesterday, how would users react?

That acceptable amount of data loss is the **RPO**.

---

# Shared Responsibility Model – Again

### Cloud Provider

Responsible for:

- Data center
- Hardware
- Network
- Infrastructure

### Customer

Responsible for:

- Data
- IAM
- Configuration
- Permissions

### Question

**If Blob Storage becomes public, whose fault is it?**

---

# Final Scenario

A company operates an online platform where the following happens:

1. The database crashes.
2. Customers cannot log in.
3. Orders from the last month are missing.

### Questions

**1. Is this an RTO or RPO problem?**

**2. How would the end customers be affected?**

**3. What should the company have done differently?**

### Hint

There are actually **two problems** here:

- **Customers cannot log in** → **RTO / Availability problem**
- **Orders from the last month are missing** → **RPO / Data recovery problem**

The company should have had an appropriate **backup and disaster recovery strategy**, with clearly defined **RTO and RPO targets**.
### Link
HA:  https://www.youtube.com/watch?v=JRbhGzGzoOA
Backup vs DR: https://www.youtube.com/watch?v=07EHsPuKXc0
Block vs File storage: https://www.youtube.com/watch?v=PmxWTTpXNLI

