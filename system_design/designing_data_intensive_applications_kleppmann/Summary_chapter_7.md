Chapter 7 of **Designing Data-Intensive Applications**, titled **"Sharding,"** tackles the challenge of breaking large datasets into smaller, manageable pieces to distribute load across multiple machines.

### **Informative Summary of Chapter 7**

*   **The Purpose of Sharding**: The primary reason for sharding (also called partitioning) is **scalability** [1, 2]. When the data volume or write throughput becomes too large for a single node, sharding allows you to spread the data and query load across a cluster of machines, achieving horizontal scaling [1, 2].
*   **Sharding by Key Range**: This approach assigns a contiguous range of keys (from a minimum to a maximum) to each shard, similar to volumes of a printed encyclopedia [3]. It allows for highly efficient range queries [4]. However, it risks creating **hot spots** (nodes with disproportionately high load) if the access pattern targets keys that are close together, such as sequential timestamps [5, 6].
*   **Sharding by Hash of Key**: To distribute load more evenly and avoid hot spots, a hash function is applied to each key, and shards are assigned a range of hash values rather than raw keys [5, 7]. While this successfully spreads out the data, it destroys the original ordering of the keys, making range queries inefficient [6].
*   **Rebalancing Shards**: As your database grows or nodes are added/removed, data and requests must be moved from one node to another to maintain fairness—a process called rebalancing [8].
    *   Using a simple `hash(key) mod N` approach is generally avoided because adding or removing a node would force almost all keys to move to new nodes [9, 10].
    *   Fully automatic rebalancing can adapt to load changes but is risky because it is an expensive operation that can overload the network and disrupt other requests [11]. Manual or semi-manual rebalancing gives administrators more control [11]. 
*   **Request Routing**: When a client wants to make a request, it needs to know which node holds the corresponding shard [12]. Systems solve this by either using a round-robin approach (where nodes forward requests to the correct owner), using a dedicated routing tier, or making the clients "shard-aware" [13, 14]. Many distributed data systems rely on coordination services like **ZooKeeper** or **etcd** to maintain the authoritative mapping of shards to nodes [15].
*   **Sharding and Secondary Indexes**: Secondary indexes complicate sharding because they don't map cleanly to the primary partition key [16]. There are two main approaches:
    *   **Local Secondary Indexes (Document-partitioned)**: Each shard maintains an index only for the records it contains [17]. Writing is fast, but reading requires sending the query to *all* shards and combining the results (a scatter/gather approach) [18].
    *   **Global Secondary Indexes (Term-partitioned)**: The secondary index is sharded separately from the primary data, based on the indexed term itself [19, 20]. Reading is fast because the client only needs to query a single shard for the index, but writing is slower and more complex since a single document write might require updating multiple index shards [21, 22].

***

