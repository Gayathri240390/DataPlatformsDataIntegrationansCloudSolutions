# Architecture Diagram – Mandatory Deliverable

Create a simple diagram of **your completed solution**. It should show the components and how they communicate; you do not need to use UML or any other advanced standard.

The diagram must show at least:

* a client or user that calls the solution
* the three simulated IoT sensors
* the REST API
* PostgreSQL for persistent history
* Redis for caching the latest measurement
* Docker Compose as the local runtime environment
* the CI pipeline
* a Kubernetes demo with Deployment, Pod replicas, and Service

Use **named arrows** to show important requests and data flows, for example `HTTP POST /measurements`, `SQL`, and `cache read/write`. It should be clear which flow is **write-heavy**, what is cached, and what must be persistent.

A simple example of the level of detail:

```text
[3 Sensors] -- HTTP POST /measurements --> [REST API]
                                                |  \
                                  SQL, history   |   \ latest value
                                                v    v
                                          [PostgreSQL] [Redis Cache]

[GitHub push] --> [CI: tests + image build]
[User] --> [Kubernetes Service] --> [Deployment: 3 Pod replicas]
```

The example is only guidance, not a template that must be copied. You can create one connected diagram or two clearly labeled views (**local Docker Compose environment** and **Kubernetes demo**). Do not make the diagram more detailed than necessary to explain the solution.

## How to Submit It to the Repository

1. Create the diagram using any tool, for example diagrams.net, Excalidraw, Visio, PowerPoint, or Figma.
2. Export it as a PNG or PDF to `docs/`.
3. Link to or embed the file here.
4. Replace this instruction with a short description of the diagram and your most important architecture choices.

Before submitting, check that the text and arrows are readable directly from GitHub and that the diagram matches the code you are actually submitting.
