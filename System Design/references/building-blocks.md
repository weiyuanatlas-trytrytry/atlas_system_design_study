# System Design Building Blocks

Use this reference when the task needs concrete component choices or scaling patterns.

## Core Components

### CDN

Use for static asset delivery, edge caching, and lowering latency for geographically distributed users.

### Load Balancer

Distributes traffic across stateless application servers. Mention health checks, failover, and session affinity only if relevant.

### API Gateway

Useful when there are many client-facing services, auth policies, or rate limits to centralize.

### Cache

Use Redis or Memcached-style caches for hot reads, session data, counters, and rate limiting. Be explicit about cache invalidation and fallback behavior.

### Database

- Relational DB: strong consistency, transactions, indexed queries, structured entities.
- NoSQL key-value/document store: very high scale, flexible schema, simpler access patterns.
- Time-series/search/graph engines: choose only when the access pattern justifies them.

### Object Storage

Use for blobs such as images, videos, and backups. Pair with CDN for delivery.

### Message Queue / Log

Use Kafka, SQS, RabbitMQ-style components to decouple producers and consumers, absorb spikes, and enable async processing.

### Search Index

Use Elasticsearch/OpenSearch-style systems for full-text search, ranking, faceting, and autocomplete.

## Common Patterns

### Read-heavy system

Start with cache + read replicas + CDN. Discuss cache warming, TTL, and stale-read tolerance.

### Write-heavy event ingestion

Start with partitioned log/queue + stateless consumers + batch/stream processing + append-friendly storage.

### Social feed

Discuss fanout-on-write versus fanout-on-read. State the scale threshold where precomputation becomes expensive.

### Messaging / chat

Discuss persistent connections, message ordering, delivery acknowledgements, offline storage, and push notifications.

### File upload / media

Discuss pre-signed uploads, object storage, metadata DB, async transcoding, and CDN.

### Multi-tenant SaaS

Discuss tenant isolation, noisy-neighbor controls, quota/rate limiting, and billing instrumentation.

## Reliability Levers

- Replication for durability and high availability
- Partitioning/sharding for scale
- Retries with backoff for transient failures
- Idempotency for duplicate requests
- Dead-letter queues for poisoned events
- Circuit breakers and timeouts for dependency failures
- Observability with logs, metrics, traces, and SLOs

## Tradeoff Reminders

- Consistency vs latency
- Simplicity vs optimization
- Precomputation vs storage cost
- Denormalization vs write complexity
- Availability vs coordination
- Global distribution vs operational complexity
