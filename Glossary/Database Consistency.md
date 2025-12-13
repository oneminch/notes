- Refers to the requirement that any transaction must change data only in allowed ways, ensuring the database remains in a valid state that satisfies predefined rules and constraints.
- Ensures that:
    - Data adheres to all defined rules, constraints, triggers, and cascades.
    - Any changes made to data are valid and do not violate the database's integrity.
    - The database remains in a correct and coherent state after transactions.

- **Types of Consistency**:
    - _Strong Consistency_
        - Ensures all nodes in a distributed system have the same data at any given time
        - Provides immediate accuracy but may result in higher latency
        - Typically used in ACID-compliant relational databases
    - _Eventual Consistency_
        - Allows for temporary inconsistencies across nodes, but guarantees data will converge to a consistent state over time
        - Offers lower latency and higher availability
        - Common in NoSQL and distributed databases

- **Implementation Mechanisms**:
    - Constraints (e.g., check constraints, key constraints)
    - Transactions
    - Locking mechanisms
    - Consensus algorithms (in distributed systems)
    - Data validation rules

- **Consistency Models**:
    - ACID (Atomicity, Consistency, Isolation, Durability)
        - Ensures strong consistency and data integrity, typically used in relational databases.
    - BASE (Basically Available, Soft state, Eventual consistency)
        - Prioritizes availability and partition tolerance over strong consistency, often used in NoSQL systems
