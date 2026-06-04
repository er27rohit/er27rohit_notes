Chapter 7 of **Designing Data-Intensive Applications**, titled **"Sharding,"** tackles the challenge of breaking large datasets into smaller, manageable pieces to distribute load across multiple machines.


### **Quality Notes: Chapter 7 - Sharding**

**1. The Purpose and Trade-offs of Sharding**
*   **Definition:** Sharding (or partitioning) is the process of splitting a large dataset into smaller subsets across multiple nodes [1]. It is primarily used for **horizontal scalability**, allowing a system to handle data volumes or write throughputs that are too large for a single machine [2].
*   **The Trade-off:** While sharding solves scale, it introduces complexity. Simple key-value lookups work well, but queries that require joining records or searching by secondary indexes across multiple shards become difficult [3]. Cross-shard distributed transactions are also usually much slower than single-node transactions and can become system bottlenecks [3].
*   **Sharding for Multitenancy:** In SaaS applications, sharding is sometimes used to give each tenant (customer) their own dedicated shard. This simplifies per-tenant backups and data restoration, and it helps satisfy regulatory compliance (like GDPR data deletion requests) [4].

**2. Sharding of Key-Value Data**
To avoid "hot spots" (where one node takes a disproportionately high amount of load while others are idle), data must be distributed evenly. There are two main approaches:
*   **Sharding by Key Range:** Assigns a contiguous range of keys to each shard (like volumes of an encyclopedia) [5]. This approach makes range scans highly efficient, but it risks creating severe hot spots if the keys are accessed sequentially, such as using timestamps for sensor data [6, 7]. 
*   **Sharding by Hash of Key:** To evenly distribute data and avoid hot spots, a hash function is applied to the key, and each shard is assigned a range of hash values [7, 8]. While this provides an even load distribution, it destroys the ordering of the keys, making range queries inefficient [9]. 

**3. Rebalancing Shards**
When data or throughput grows, or nodes fail, shards must be moved between nodes to rebalance the cluster.
*   **The Danger of Hash Modulo N:** Using the formula `hash(key) % N` (where N is the number of nodes) is a bad approach. If the number of nodes `N` changes, almost all keys will need to be moved to different nodes, making rebalancing prohibitively expensive [10, 11].
*   **Fixed Number of Shards:** A better approach is to create many more shards than there are nodes. When a new node is added, it simply steals a few entire shards from the existing nodes until the load is fair again. The mapping of keys to shards never changes; only the mapping of shards to nodes changes [12, 13].
*   **Dynamic Partitioning:** Used mostly with key-range sharding, where a shard is automatically split in half once it exceeds a certain configured size [14]. 
*   **Automatic vs. Manual Rebalancing:** While fully automated rebalancing is convenient, it can be unpredictable and severely overload the network, impacting the performance of live requests [15, 16]. Keeping a "human in the loop" to approve manual rebalancing is slower but helps prevent operational surprises [17].

**4. Request Routing**
When a client makes a request, it needs to know which node holds the relevant shard [17, 18]. 
*   **Routing Approaches:** Clients can either (1) contact any node, which then forwards the request to the correct node; (2) send requests to a dedicated routing tier (like a shard-aware load balancer); or (3) be explicitly aware of the sharding logic and connect directly to the right node [19, 20].
*   **Coordination Services:** To maintain an authoritative mapping of which shards live on which nodes, many distributed systems rely on external consensus services like **ZooKeeper** or **etcd** [21, 22]. When a shard changes ownership, ZooKeeper instantly notifies the routing tier to keep the network mapping up to date [21]. 

**5. Sharding and Secondary Indexes**
Because secondary indexes don't uniquely identify records, they don't map neatly to a primary partition key [23]. They are typically sharded in one of two ways:
*   **Local Secondary Indexes (Document-partitioned):** Each shard maintains its own secondary index covering only the documents stored in that specific shard [24, 25]. Writes are very fast because they only modify one local shard. However, reads are expensive because a query must be sent to *all* shards and the results combined (a scatter/gather approach) [26, 27].
*   **Global Secondary Indexes (Term-partitioned):** The secondary index is sharded globally, independently of the primary key data [28]. For example, all red cars are indexed in Shard 0, while all silver cars are indexed in Shard 1, regardless of where the car's actual data lives [28, 29]. Reads are very fast because you only need to query the single shard containing your term. However, writes are slower and more complex, as a single database write might require updating secondary indexes across multiple different shards (usually done asynchronously) [29, 30].


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

