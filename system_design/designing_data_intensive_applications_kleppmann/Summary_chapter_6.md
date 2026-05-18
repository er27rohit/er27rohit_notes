Chapter 6 of **Designing Data-Intensive Applications**, titled **"Replication,"** focuses on the challenges of keeping multiple copies of the same data on different machines.

### **Informative Summary of Chapter 6**

*   **Goals of Replication**: Replication is used to reduce **latency** (keeping data geographically close to users), increase **availability** (allowing the system to work if parts fail), and scale **read throughput** [1, 2].
*   **Single-Leader Replication**: One node is designated the leader and handles all writes, while other nodes (followers) apply a stream of changes from the leader [3, 4]. It is widely used in databases like PostgreSQL and MySQL [5].
*   **Synchronous vs. Asynchronous**: In **synchronous replication**, the leader waits for followers to confirm the write, ensuring no data loss but risking a system-wide block if a follower fails [6]. **Asynchronous replication** is faster but can lead to data loss if the leader crashes before changes are replicated [7].
*   **Handling Node Failures**: Follower failure is handled via **catch-up recovery** using logs [8]. Leader failure requires **failover**, where a follower is promoted to leader—a process fraught with risks like split-brain (two nodes believing they are the leader) [9-11].
*   **Replication Lag Anomalies**:
    *   **Read-after-write consistency**: Ensures a user always sees their own updates immediately [12].
    *   **Monotonic reads**: Guarantees that a user won't see data "moving backward" in time [13].
    *   **Consistent prefix reads**: Ensures that causal sequences (like a question and its answer) are always seen in the correct order [14, 15].
*   **Multi-Leader Replication**: Multiple nodes accept writes. This is beneficial for **multi-region setups** and **offline-capable apps** (like calendars or collaborative editors) but requires complex **conflict resolution** [16-18].
*   **Conflict Resolution**: Strategies include **Last Write Wins (LWW)**, which is simple but loses data, and more robust algorithms like **Conflict-free Replicated Datatypes (CRDTs)** or **Operational Transformation (OT)** [19-21].
*   **Leaderless Replication (Dynamo-style)**: Any replica can accept writes. Clients use **quorums** ($w + r > n$) to ensure that at least one of the nodes they read from has the most recent successful write [22, 23].
*   **Keeping Replicas Sync**: Leaderless systems use **read repair** (fixing data during a read) and **hinted handoff** (temporarily holding writes for a down node) to stay updated [24, 25].
*   **Concurrency Detection**: Algorithms like **happens-before** and **version vectors** are used to determine whether two writes are concurrent or if one causally depends on the other [26, 27].

***