# Reddit Tech Stack and Distributed Systems Architecture

This document serves as the comprehensive report and script for the **Reddit Tech Stack & Distributed Systems** presentation. It provides detailed information on every topic covered in the slides, including the sources where this information was found.

---

## 1. What is Reddit?

Reddit is a social news aggregation, content rating, and discussion website. Users submit content (links, text, images, or videos) to communities called **subreddits**, and other users vote content up or down and engage in threaded discussions. Founded in 2005, Reddit is now one of the most visited websites in the world, with more than 100,000 active communities covering virtually every topic imaginable.

**Sources:** [StackShare](https://stackshare.io/reddit/reddit), [Himalayas](https://himalayas.app/companies/reddit/tech-stack)

---

## 2. Reddit at Scale

Understanding the scale of Reddit is critical to appreciating why it requires such a complex distributed backend:

- **116 million** Daily Active Users (DAUs globally, representing a +19.3% YoY growth)
- **443.8 million** Weekly Active Users (WAUs globally)
- **2.2+ billion** visits per month globally (78% mobile, 22% desktop)
- **56.7%** of daily users browse while logged out (anonymous users)
- **3.14 billion** comments & interactions generated annually
- **1.4 billion** native video views per month
- **70+ million** searches per week on-platform
- **100,000+** active subreddits (with over 500 subreddits having >1 million members)

This volume of traffic introduces the **distribution transparency** problem: from the user's perspective, Reddit looks and behaves like a single, unified system. However, behind the scenes, thousands of machines must coordinate seamlessly. This is the core goal of distributed systems design — but achieving it while maintaining performance is a major engineering challenge. Transparency types include:
- **Access transparency**: users don't know if content is local or remote.
- **Location transparency**: services don't know where data is physically stored.
- **Failure transparency**: a server crash does not interrupt the user experience.
- **Replication transparency**: multiple copies of data exist, but users see only one.

**Sources:** [Interteam Marketing](https://www.interteammarketing.com/blog/reddit-statistics-2026), [Himalayas](https://himalayas.app/companies/reddit/tech-stack), [ByteByteGo](https://bytebytego.com/guides/reddits-core-architecture/)

---

## 3. Why a Distributed System?

A distributed system is designed to achieve three core goals, each of which Reddit must satisfy simultaneously:

### Fault Tolerance
- No single point of failure: if one server goes down, others continue serving traffic.
- Data is replicated across multiple database nodes and data centers.
- Services are containerized and restart automatically if they crash.

### Scalability
- Services are scaled horizontally: more instances are added as traffic grows.
- Traffic spikes (e.g., major AMA sessions or sporting events) are handled by Kubernetes auto-scaling.
- Microservices allow individual components to scale independently.

### Efficiency
- A multi-layer caching strategy (Memcached + Redis) absorbs the vast majority of read traffic before it reaches the database.
- Asynchronous processing (RabbitMQ, Kafka) offloads expensive work from the HTTP request cycle.
- Rewriting critical services in Go dramatically reduces CPU and memory usage compared to Python.

**The Transparency Trade-off:** Achieving full distribution transparency conflicts with consistency and performance. Reddit must make deliberate choices about which transparency properties to sacrifice under load (e.g., accepting eventual consistency in Cassandra to gain high availability).

**Sources:** [ByteByteGo](https://bytebytego.com/guides/reddits-core-architecture/)

---

## 4. Reddit Core Architecture (Architecture Diagram)

The architecture diagram shows Reddit's backend as a collection of specialized, interconnected layers:

1. **Client Layer**: Web browsers and mobile apps connect through the GraphQL Gateway.
2. **API Layer (GraphQL Federation)**: A single gateway routes requests to Go microservices.
3. **Application Layer (Go Microservices)**: Independent services for posts, comments, user accounts, search, and more.
4. **Data Layer**: PostgreSQL (ThingDB pattern) for relational data, Cassandra for high-availability NoSQL.
5. **Cache Layer**: Memcached and Redis absorb the majority of read traffic.
6. **Messaging Layer**: Kafka (event streaming) and RabbitMQ (background job queues).
7. **Infrastructure Layer**: AWS (S3, EKS), Kubernetes for orchestration.

**Sources:** [ByteByteGo](https://bytebytego.com/guides/reddits-core-architecture/)

---

## 5. Languages & API Layer

### Python (Legacy Monolith — `r2`)
Reddit was originally built in Common Lisp and then rewritten in Python in 2005. The Python monolith (called `r2`) handled all routing, business logic, and templating for over a decade. However, as traffic grew, Python's performance limitations became a bottleneck, particularly in CPU-bound workloads like feed generation and comment tree rendering.

### Go (Golang — Modern Microservices)
To address these limitations, Reddit began rewriting performance-critical services in Go. Go's goroutines provide lightweight concurrency, and its compiled nature yields significantly lower CPU usage and API latency. Services like the comment rendering engine, post-delivery feed, and search indexing have been migrated to Go microservices.

### GraphQL Federation
Reddit uses a GraphQL Gateway as the single entry point for all clients (web, iOS, Android). Behind the gateway, the API is split into Domain Graph Services (DGS) subgraphs — each owned by a specific team (e.g., subreddit subgraph, user subgraph, post subgraph). This provides **location transparency** for clients: they query one unified API without knowing which microservice handles each field. Teams can deploy subgraph updates independently without coordinating with other teams.

**Sources:** [StackShare](https://stackshare.io/reddit/reddit), [Himalayas](https://himalayas.app/companies/reddit/tech-stack)

---

## 6. Databases & Caching

### PostgreSQL — ThingDB Pattern
PostgreSQL is Reddit's primary persistent store. Rather than using a traditional normalized relational schema, Reddit's legacy system used a custom Entity-Attribute-Value (EAV) design called **ThingDB**:
- Two main tables: `data` (attributes of a "thing") and `links` (associations between things).
- A "thing" can be a post, comment, account, or subreddit.
- This schema-less design allowed engineers to add new attributes without database migrations, at the cost of moving join logic to the application layer.
- Read replicas provide **replication transparency** and fault tolerance.

### Apache Cassandra
Cassandra is a distributed, wide-column NoSQL database with multi-datacenter replication. Reddit uses Cassandra for data requiring high availability and horizontal scalability, such as vote counts, traffic histories, and real-time user status. Cassandra's tunable consistency model allows Reddit to choose between stronger consistency (higher latency) and higher availability (eventual consistency), representing a direct CAP theorem trade-off.

### Memcached
Memcached is a simple, in-memory key-value store used as the first caching layer in front of PostgreSQL. It caches rendered post content, session tokens, and user metadata. By serving the vast majority of read requests from memory, Memcached dramatically reduces database load and improves response times.

### Redis
Redis is used for structured caching and as a pub/sub message broker. It stores leaderboards, rate-limiting counters, distributed locks, and moderator permission sets. Redis also acts as an event store for Change Data Capture (CDC) events, bridging the gap between PostgreSQL changes and downstream consumers.

**Sources:** [ByteByteGo](https://bytebytego.com/guides/reddits-core-architecture/)

---

## 7. Async Messaging & Infrastructure

### RabbitMQ — Background Job Queue
RabbitMQ is an AMQP-based message broker used for fire-and-forget background tasks. When a user votes, submits a post, or triggers a notification, the action is placed on a RabbitMQ queue and consumed asynchronously by worker processes. This decouples the HTTP request-response cycle from expensive operations, improving perceived performance and throughput.

### Apache Kafka + Debezium (Change Data Capture)
Kafka is a distributed event streaming platform used for real-time data pipelines. Reddit uses **Debezium**, which monitors PostgreSQL's Write-Ahead Log (WAL), and publishes change events (inserts, updates, deletes) to Kafka topics. Downstream services subscribe to these topics to:
- Update the **search index** (Elasticsearch)
- Detect **spam and policy violations**
- Feed **analytics pipelines**
- Trigger **cache invalidations**

This pattern achieves **eventual consistency** across the distributed system without requiring services to directly communicate with the database.

### AWS & Kubernetes (EKS)
Reddit operates on Amazon Web Services. Storage (media, backups) lives on S3. Compute runs on EC2 instances managed by **Kubernetes (EKS)**, which handles auto-scaling, rolling deployments, and health monitoring. If a pod crashes, Kubernetes restarts it automatically, providing **failure transparency**.

### The "Sister Datastore" (Tap-Compare) Migration Pattern
When migrating from the Python monolith to Go microservices, Reddit uses a safe migration strategy:
1. Provision a duplicate ("sister") database/cache alongside production.
2. The old service writes to both stores simultaneously ("tapping").
3. A validation engine compares reads from both stores ("comparing").
4. Once the new service produces matching results at production scale, the old system is deprecated.

This pattern ensures zero-downtime migrations with full verification before cutover.

**Sources:** [ByteByteGo](https://bytebytego.com/guides/reddits-core-architecture/), [Himalayas](https://himalayas.app/companies/reddit/tech-stack)

---

## References (Bibliography)

1. StackShare. *Reddit Tech Stack*. https://stackshare.io/reddit/reddit (accessed 2026-08-23).
2. Himalayas. *Reddit Tech Stack and Tools*. https://himalayas.app/companies/reddit/tech-stack (accessed 2026-08-23).
3. ByteByteGo. *Reddit's Core Architecture*. https://bytebytego.com/guides/reddits-core-architecture/ (accessed 2026-08-23).
4. Interteam Marketing. *Reddit Statistics 2026: Traffic, Users, Ads and Revenue*. https://www.interteammarketing.com/blog/reddit-statistics-2026 (accessed 2026-08-23).
