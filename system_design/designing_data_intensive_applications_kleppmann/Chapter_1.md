Chapter 1 of **Designing Data-Intensive Applications**, titled "Trade-Offs in Data Systems Architecture," focuses on the foundational choices and competing concerns involved in building modern data systems [1, 2].

Below is a summary of the key concepts discussed in the chapter:

*   **Data-Intensive Applications**: An application is considered data-intensive if its primary challenges relate to data management—such as volume, complexity, or rate of change—rather than raw computation power [3].
*   **Operational (OLTP) vs. Analytical (OLAP) Systems**:
    *   **Operational systems (Online Transaction Processing)** serve end-users and are optimized for low-latency, high-volume "point queries" that read or update individual records [4-6].
    *   **Analytical systems (Online Analytical Processing)** serve analysts and data scientists, focusing on aggregating large numbers of records to identify trends and patterns [4, 6, 7].
*   **Data Warehousing and ETL**: To prevent heavy analytical queries from impacting the performance of operational systems, companies extract data from OLTP databases, transform it into an analysis-friendly schema, and load it into a separate **data warehouse** via a process known as **ETL (Extract–Transform–Load)** [8, 9].
*   **Systems of Record vs. Derived Data**:
    *   A **system of record** (source of truth) holds the authoritative version of a fact; new data is written here first [10].
    *   **Derived data systems** (like caches, search indexes, or materialized views) take existing information and transform it to improve read performance or enable different types of queries [11, 12].
*   **Cloud vs. Self-Hosting**: Using cloud services outsources software operation to a provider to save time and money [13]. A key trend in **cloud native** architecture is the **separation of storage and compute**, allowing these resources to scale independently [14, 15].
*   **Distributed vs. Single-Node Systems**:
    *   **Distributed systems** (multiple machines or "nodes" communicating via a network) are used for scalability, high availability, and fault tolerance [16, 17].
    *   However, they introduce significant complexity, such as **network failures**, variable latency, and difficult data consistency challenges [18, 19]. Whenever possible, starting on a single node is often simpler and cheaper [20, 21].
*   **Microservices**: This architecture divides software into multiple services that communicate via APIs (usually over HTTP), allowing teams to update parts of a system independently [20, 22].
*   **Data Systems and Society**: Architecture is increasingly influenced by legal requirements, such as the **GDPR**, which mandates user rights like the "right to be forgotten" [23, 24]. Engineers must balance business needs against ethical responsibilities and user rights [2, 25].
