Chapter 2 of **Designing Data-Intensive Applications**, titled "Defining Nonfunctional Requirements," moves from high-level architecture to the specific metrics and goals used to judge a data system's success [1, 2].


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



### **Summary of Chapter 2**

*   **Reliability (Fault Tolerance)**: A reliable system continues to work correctly even when things go wrong ("faults") [3, 4]. Faults include **hardware failures** (disk crashes, power outages), **software faults** (runaway processes, bugs), and **human error** (misconfigurations) [5, 6]. The goal is usually fault tolerance rather than fault prevention [4].
*   **Scalability**: This is the system's ability to cope with increased load [7]. It is not a one-dimensional label; instead, it involves identifying **load parameters**—such as the ratio of reads to writes or the number of simultaneous users—and determining how to add resources to keep performance stable [8-10].
*   **Case Study: Social Network Timelines**: The chapter uses the example of building a service like **X (Twitter)** to illustrate scaling challenges [11]. It compares **write-side fan-out** (materializing a timeline when a tweet is posted) with **read-side joins** (gathering tweets when a user loads their timeline) [12, 13]. A major bottleneck is the **"celebrity problem"**, where a single post from a user with millions of followers can overwhelm write-side fan-out infrastructure [13, 14].
*   **Performance Metrics**: To describe performance, we distinguish between **throughput** (the volume of work per second) and **response time** (what the client sees) [15]. Rather than using averages, engineers should focus on **percentiles** (such as p95 and p99) to understand **tail latency**, which often affects the most active or valuable users [16, 17].
*   **Maintainability**: This refers to the long-term cost of software [3]. It has three main pillars: **operability** (making it easy for operations teams to keep the system running), **simplicity** (removing accidental complexity), and **evolvability** (making it easy to adapt the system to new requirements) [18, 19].
