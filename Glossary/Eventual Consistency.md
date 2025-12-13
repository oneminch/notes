- A consistency model used in distributed systems where data across multiple nodes or replicas is not immediately synchronized, but is guaranteed to become consistent over time when no new updates are made.
    - **Temporary Inconsistency**: Different replicas may return different values for the same data immediately after an update
    - **Convergence**: All replicas will eventually converge to the same value
    - **No Blocking**: Read and write operations don't need to wait for all replicas to synchronize
    - **High Availability**: Systems remain responsive even during network partitions

- **Benefits**
    - Fast reads/writes
    - Easier to scale horizontally
- **Drawbacks**
    - Temporary stale data
    - Possible conflicts & complex conflict resolution

- **How It Works**
    1.  A write operation is applied to one replica (the primary)
    2.  The system immediately acknowledges the write to the client
    3.  Updates are asynchronously propagated to other replicas
    4.  Eventually, all replicas contain the same data

- **Examples**
    - **DNS**: Domain name records take time to propagate globally
    - **NoSQL databases**: DynamoDB, Cassandra, Workers KV
    - **Social media**: Like counts, follower updates
    - **Cloud storage**: S3, eventual consistency for object metadata
