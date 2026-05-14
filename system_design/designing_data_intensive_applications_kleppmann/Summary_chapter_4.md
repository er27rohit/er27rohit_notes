Chapter 4 of **Designing Data-Intensive Applications**, titled **"Storage and Retrieval,"** focuses on the internal mechanisms databases use to store data and find it again. Understanding these "under-the-hood" algorithms is crucial for selecting the right database and tuning it for specific workloads [1].

### **Informative Summary of Chapter 4**

*   **Log-Structured Storage (LSM-Trees)**: 
    *   This family of storage engines treats the database as an **append-only log**. Appending is the simplest and fastest possible write operation [2, 3].
    *   **SSTables (Sorted Strings Tables)**: Data is stored in segments where key-value pairs are sorted by key. This allows for efficient merging and range queries [4, 5].
    *   **Memtables and Compaction**: New writes go into an in-memory **memtable**. When it gets too big, it’s written to disk as an SSTable segment. A background **compaction** process merges segments and discards overwritten or deleted values [5, 6].
    *   **Bloom Filters**: To prevent slow reads when a key doesn't exist (which would otherwise require checking every segment on disk), engines use **Bloom filters**—a probabilistic data structure that can quickly tell you if a key is *not* present [7, 8].
*   **Update-in-Place Storage (B-Trees)**:
    *   B-trees are the most widely used indexing structure. Unlike logs, they break the database into fixed-size **pages** (usually 4 KB) and update data by overwriting these pages in place [9, 10].
    *   They provide consistent **O(log n)** performance for reads and are the standard for most relational databases [9].
*   **LSM-Trees vs. B-Trees**: As a rule of thumb, **LSM-trees are better for write-heavy** applications because they turn random writes into sequential ones, while **B-trees are generally faster for reads** [11].
*   **Column-Oriented Storage (OLAP)**:
    *   While OLTP databases are usually **row-oriented** (storing all values for one row together), analytical databases often use **column-oriented storage** [12, 13].
    *   By storing all values of a single column together, a query only needs to read the specific columns it requires, drastically reducing disk I/O for massive datasets [13, 14].
    *   This layout allows for extreme **compression** (e.g., bitmap encoding), further boosting performance [15, 16].
*   **Indexing for Search and AI**:
    *   **Full-Text Search**: Uses **inverted indexes** to map terms to document IDs [17].
    *   **Vector Search**: For AI applications, specialized indexes like **IVF** or **HNSW** are used to perform "approximate nearest neighbor" searches in high-dimensional vector spaces [18, 19].

***

