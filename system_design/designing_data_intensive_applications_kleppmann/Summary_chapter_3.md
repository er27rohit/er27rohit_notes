Chapter 3 of **Designing Data-Intensive Applications**, titled **"Data Models and Query Languages,"** explores how the way we represent data profoundly affects how we perceive and solve engineering problems [1, 2]. 

Here are the structured, high-quality notes for Chapter 2, focusing on the core definitions, mechanisms, and metrics used to evaluate and design data-intensive systems.

### **Quality Notes: Chapter 2 - Defining Nonfunctional Requirements**

**1. Functional vs. Nonfunctional Requirements**
*   **Functional Requirements:** What the application is supposed to do (e.g., the required screens, buttons, and specific operations) [1].
*   **Nonfunctional Requirements:** The properties that determine how well the system operates, such as being fast, reliable, secure, legally compliant, and easy to maintain [1]. 

**2. Case Study: Social Network Timelines and Fan-Out**
*   **The Problem:** Building a home timeline requires gathering posts from all the accounts a user follows [2]. Doing this on the read-side via a relational join is too slow and expensive for high-volume systems [2, 3].
*   **Materialization (Write-Side Fan-out):** Instead of computing the timeline on the fly, systems proactively precompute and update a materialized view (a cache) of the timeline whenever a new post is made [4]. One user's post is pushed to the timelines of all their followers, a multiplication effect known as "fan-out" [3].
*   **The Celebrity Problem:** For highly followed accounts, write-side fan-out causes millions of immediate writes, overwhelming the system [4]. Systems solve this by handling celebrity posts separately, fetching them at read-time and merging them with the materialized timeline [4].

**3. Describing Performance**
*   **Throughput vs. Response Time:** **Throughput** is the rate of processing (e.g., requests or data volume per second) [5]. **Response time** is the total elapsed time a client waits for a response (including queueing and network delays) [5]. 
*   **Latency:** Often used interchangeably with response time, but strictly refers to the time a request spends "latent" or waiting (e.g., network delay), not actively being processed [6].
*   **Percentiles and Tail Latency:** Averages (means) are misleading because they hide outliers [7]. Engineers should measure percentiles (e.g., p95, p99, p99.9) to understand the worst-case "tail latencies" [8]. The p99.9 represents the slowest 1 in 1,000 requests, which often affect the most valuable power-users [8, 9].
*   **SLOs and SLAs:** Service Level Objectives (SLOs) and Service Level Agreements (SLAs) define the expected performance (like a p99 under 1 second) and form a contract specifying consequences if the system falls short [10].
*   **Metastable Failures:** If a system is overloaded, slow response times can cause clients to time out and resend requests. This causes a "retry storm," amplifying the load further in a vicious cycle known as a metastable failure [11].

**4. Reliability and Fault Tolerance**
*   **Definition:** A reliable system continues to work correctly, performing its expected function, even when things go wrong (faults) [11]. 
*   **Hardware Faults:** Hard drives crash, RAM corrupts, and datacenters lose power [12]. These are typically uncorrelated and are mitigated by adding hardware redundancy, which also enables zero-downtime "rolling upgrades" [13].
*   **Software Faults:** Bugs in code are highly correlated and can cause cascading failures across many nodes [13, 14]. They often lie dormant until triggered by unusual circumstances [14].
*   **Human Errors:** Humans are unreliable [15]. Systems must be designed to tolerate human mistakes (e.g., providing easy rollbacks) and organizations should foster a culture of "blameless postmortems" rather than punishing individuals [15-17].

**5. Scalability**
*   **Definition:** Scalability is the system's ability to cope with increased load by adding computing capacity [16]. 
*   **Vertical vs. Horizontal Scaling:** Moving a system to a larger, more powerful machine is called *vertical scaling* (scaling up) [18]. Spreading the load across multiple smaller machines is called *horizontal scaling* (scaling out) and relies on shared-nothing architectures [18]. 

**6. Maintainability**
Because the majority of software cost goes into ongoing maintenance, systems must be built for the long term [19, 20]. This rests on three pillars:
*   **Operability:** Making life easy for operations teams by providing good monitoring and routine maintenance tools [21].
*   **Simplicity:** Managing complexity by using good abstractions and consistent patterns to remove "accidental complexity" [22].
*   **Evolvability:** Designing the system so that it is easy to adapt to unanticipated future changes and requirements [21].



### **Informative Summary of Chapter 3**

*   **Layering of Data Models**: Modern applications are built on layers of abstractions. As a developer, you model the real world in terms of objects or data structures; those are translated into general-purpose models like **JSON documents or relational tables** [2, 3]. The database engine then translates those into bytes on disk or in memory [3]. 
*   **Relational vs. Document Models**: 
    *   **Relational Model (SQL)**: Organizes data into tables (relations) and rows (tuples) [4]. It is best suited for data with complex relationships, providing superior support for **joins** and many-to-one or many-to-many relationships [5, 6].
    *   **Document Model (NoSQL)**: Stores data in self-contained documents (like JSON). It excels at **one-to-many relationships** (tree structures) where the entire tree is usually loaded at once [5, 7]. It offers **data locality**, as all relevant info is in one place, reducing disk seeks [8, 9].
*   **Schema Flexibility**:
    *   **Schema-on-write (Relational)**: The database ensures all data conforms to a specific structure before it is stored [10, 11]. 
    *   **Schema-on-read (Document)**: The database is flexible and can store a mixture of formats; the application code handles the structure when the data is retrieved [10, 12].
*   **Normalization vs. Denormalization**: 
    *   **Normalization** removes redundancy to make writes faster and updates easier, but it requires "joins" for reading [13, 14]. 
    *   **Denormalization** (often used in analytical systems) duplicates data to make reading faster by avoiding joins, but it makes writes more expensive and complex to keep consistent [13, 14].
*   **Declarative vs. Imperative Query Languages**: SQL is **declarative**, meaning you tell the database *what* you want, and its optimizer decides the most efficient way to get it (including parallel execution) [4, 15]. Imperative code (like a manual loop in Java/Python) requires the programmer to specify exactly *how* to step through the data [4, 15].
*   **Graph-Like Data Models**: When relationships in your data become too complex for the relational model (where everything is potentially related to everything), **graph models** (like Property Graphs or Triple-stores) are more natural [16, 17]. They use query languages like **Cypher or SPARQL** to traverse multiple "hops" efficiently [17, 18].
*   **Event Sourcing and CQRS**: This model treats the **append-only log of immutable events** as the system of record [12, 19]. Read-optimized views of that data are then derived through a process called **Command Query Responsibility Segregation (CQRS)** [12, 20].
*   **Data Models for Analytics**: Data warehouses often use **star or snowflake schemas** [21]. These consist of massive **fact tables** representing events, which link to smaller **dimension tables** representing the "who, what, where, when, and why" of those events [22].

