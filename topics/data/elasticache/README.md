# ElastiCache
- **ElastiCache:** fully managed service for Redis or Memcached
- **Cache:** in-memory DB with high performance and low latency to reduce read traffic on databases
    - Caching allows for stateless applications but storing the state
- Many code changes required if implementing caching for the first time
- App will query the cache for data
    - **Cache Hit:** data exists in cache
    - **Cache Miss:** data does not exist in cache, app goes to DB for data, then writes to cache
- Must have an invalidation strategy to make sure its most recent data by implementing a TTL
- Supports reader endpoints and in-flight encryptions

## Caching Patterns
- **Lazy Load:** read data stored, get state
- **Write Through:** add/update cache when written to DB, not stale data
- **Session Store:** store session data with TTL

## Redis
- Multi-AZ with auto-failover, add read replicas for scale, very HA
- Data durability using AOF persistence, with backup and restore features
- Security via IAM policies and Redis Auth
- **Redis Auth:** credentials created at cluster, extra layer of security
- Supports sets and sorted sets
- **Redis Sorted Sets:** sorted sets that can be stored in memory that guarantees uniqueness and ordering in real-time
- Use case: gaming leaderboard

## Memcached
- multi-node for partioning of data via sharding, high performance via multi-threading
- no replication, no HA, non-persistent which creates risk for data loss
- serverless back up and restore
- security via IAM policies and SASL auth
