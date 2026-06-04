Chapter 8 of **Designing Data-Intensive Applications**, titled **"Transactions,"** explores the vital mechanism that databases use to simplify error handling and concurrency issues for developers.



### **Quality Notes: Chapter 8 - Transactions**

**1. The Purpose and Meaning of ACID**
*   **The Goal of Transactions:** Transactions exist to simplify the programming model for applications accessing a database, collapsing various error scenarios (network interruptions, crashes, concurrent writes) into two possible outcomes: commit or abort [1].
*   **Atomicity:** If an error occurs halfway through a transaction, the database abandons it entirely and rolls back any partial writes, avoiding a "half-finished" or inconsistent state [2, 3]. 
*   **Consistency:** This refers to application-specific invariants (e.g., in an accounting system, credits and debits must balance) that must always remain true [4]. 
*   **Isolation:** Ensures that concurrently executing transactions are isolated from one another and do not step on each other's toes, ideally acting as if they ran serially [5].
*   **Durability:** A guarantee that once a transaction commits successfully, any data it has written will not be forgotten or lost, even in the event of hardware faults or database crashes [6].

**2. Weak Isolation Levels**
Because providing perfect isolation (serializability) has a heavy performance cost, databases commonly offer weaker isolation levels that protect against some concurrency issues but not all [7].
*   **Read Committed:** The most basic isolation level, which guarantees no *dirty reads* (seeing uncommitted data) and no *dirty writes* (overwriting uncommitted data) [8, 9]. Databases usually prevent dirty writes using row-level locks and prevent dirty reads by remembering the old committed value while a new value is being written [10, 11].
*   **Snapshot Isolation (Repeatable Read):** Solves the problem of *read skew* (nonrepeatable reads), ensuring that a transaction sees a consistent snapshot of the database from a particular point in time, even if other transactions are modifying data [12-14]. This is ideal for long-running read queries like backups and analytics [14]. It is typically implemented using **Multiversion Concurrency Control (MVCC)**, where the database keeps multiple committed versions of a row side by side [15]. A core principle of MVCC is that *readers never block writers, and writers never block readers* [16].

**3. Concurrency Race Conditions**
*   **Lost Updates:** Occurs when two concurrent transactions read the same data, modify it, and write it back, causing one update to overwrite and lose the other [17]. This is typically mitigated using atomic write operations, explicit locking (e.g., `SELECT FOR UPDATE`), or compare-and-set operations [18-20].
*   **Write Skew:** A subtle anomaly where two concurrent transactions read the same objects but update different ones, violating an invariant [21, 22]. A classic example is two on-call doctors successfully taking themselves off call simultaneously because they both checked the schedule concurrently and saw the other doctor was still on call [21].
*   **Phantoms:** Occurs when a transaction's write changes the results of another transaction's search query [23, 24]. Phantoms often cause write skew, especially when an application checks for the *absence* of rows (where locks cannot be attached) and then proceeds to insert a row [25].

**4. Serializability**
Serializability is the strongest isolation level, preventing all possible race conditions by guaranteeing the end result is the same as if transactions had executed one at a time [26]. It is implemented in three main ways [27]:
*   **Actual Serial Execution:** Executing transactions sequentially on a single thread [27]. This works well if transactions are very fast and the active dataset fits entirely in memory [28, 29]. To avoid waiting for network I/O, the entire transaction code is often submitted ahead of time as a stored procedure [28].
*   **Two-Phase Locking (2PL):** The traditional, pessimistic approach where writers block other writers *and* readers, and readers block writers [16]. It consists of a growing phase (acquiring shared or exclusive locks) and a shrinking phase (releasing all locks at the end of the transaction) [30]. To prevent phantoms, 2PL relies on **index-range locks** (next-key locking) [31]. Due to heavy lock contention and deadlocks, its performance is typically poor [24, 32].
*   **Serializable Snapshot Isolation (SSI):** An optimistic concurrency control algorithm built on top of MVCC [33]. It allows transactions to proceed without blocking; however, when a transaction attempts to commit, the database checks if any isolation rules were violated (e.g., detecting stale MVCC reads or detecting writes that affected prior reads) and aborts the transaction if necessary [33-36].

**5. Distributed Transactions and Two-Phase Commit (2PC)**
When a transaction spans multiple nodes or shards, achieving atomicity is highly challenging because some nodes might commit while others fail, leading to inconsistent states [37, 38].
*   **Two-Phase Commit (2PC):** A protocol that relies on a coordinator (transaction manager) to orchestrate the commit across all participating nodes [39]. In Phase 1, the coordinator asks all nodes to prepare to commit; if all vote yes, it proceeds to Phase 2 and issues the actual commit request [39, 40].
*   **The Coordinator Failure Problem:** If the coordinator crashes after participants vote yes but before sending the Phase 2 commit request, the participants are left "in doubt" [41, 42]. They cannot unilaterally abort or commit, so they must hold their locks until the coordinator recovers, which can severely severely block other transactions and impact system availability [43, 44].





### **Informative Summary of Chapter 8: Transactions**

*   **The Purpose of Transactions**: In distributed systems, things can go wrong in many ways—software can crash, networks can disconnect, and concurrent writes can overwrite each other [1]. A transaction groups several reads and writes into a single logical unit, allowing the application to pretend that errors and concurrency don't exist [2, 3]. If the transaction fails, it can be aborted and safely retried [4].
*   **The Meaning of ACID**: The safety guarantees of transactions are famously summarized by the acronym ACID [5]:
    *   **Atomicity**: It ensures that a transaction is an "all-or-nothing" operation. If an error occurs halfway through, the database aborts the transaction and rolls back any partial writes [4, 6].
    *   **Consistency**: This refers to application-specific invariants (e.g., in an accounting system, credits and debits must balance). If a transaction starts with valid data, it must leave the data in a valid state [7].
    *   **Isolation**: Ensures that concurrently executing transactions do not step on each other's toes [8, 9].
    *   **Durability**: A promise that once a transaction has committed successfully, any data it has written will not be forgotten, even in the event of a hardware fault or database crash [10].
*   **Weak Isolation Levels**: Because perfect isolation (serializability) has a heavy performance cost, databases often provide weaker isolation levels that protect against *some* concurrency issues but not all [11]:
    *   **Read Committed**: Guarantees no dirty reads (seeing uncommitted data) and no dirty writes (overwriting uncommitted data) [12]. 
    *   **Snapshot Isolation (Repeatable Read)**: Essential for long-running read queries like backups and analytics. It ensures that a transaction sees a consistent snapshot of the database from a particular point in time, even if other transactions are modifying data [11]. It is commonly implemented using **Multiversion Concurrency Control (MVCC)**, where the database keeps multiple committed versions of an object [13]. The core principle here is that *readers never block writers, and writers never block readers* [14].
*   **Concurrency Race Conditions**:
    *   **Lost Updates**: Occurs when two transactions read the same data and try to update it concurrently, causing one update to be overwritten and lost [8, 15].
    *   **Write Skew**: A more subtle anomaly where two transactions read the same objects but update different ones, violating an invariant (e.g., two on-call doctors successfully requesting leave at the same time because they both checked the schedule concurrently) [16, 17]. 
*   **Serializability**: The strongest isolation level, which guarantees that even though transactions may execute concurrently, the end result is the same as if they had executed serially (one at a time) [3, 18]. It is implemented in three main ways:
    *   **Actual Serial Execution**: Simply executing transactions one by one on a single thread. This works well if transactions are very fast and data fits in memory [18, 19].
    *   **Two-Phase Locking (2PL)**: The traditional approach where locks are used aggressively. Writers block other writers *and* readers, and readers block writers [14, 18]. This provides strong safety but suffers from poor performance and variable latency [18].
    *   **Serializable Snapshot Isolation (SSI)**: A newer, optimistic concurrency control algorithm. It allows transactions to proceed without blocking; however, when a transaction attempts to commit, the database checks if any isolation rules were violated and aborts it if necessary [14, 18, 20].
*   **Distributed Transactions and 2PC**: When transactions span multiple shards or different databases, they require a **Two-Phase Commit (2PC)** protocol to ensure atomicity across the system [21, 22]. However, 2PC is often criticized for its performance penalties and operational complexities [21].

***
