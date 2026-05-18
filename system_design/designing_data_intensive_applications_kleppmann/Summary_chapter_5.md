Chapter 5 of **Designing Data-Intensive Applications**, titled **"Encoding and Evolution,"** explores the critical challenge of changing data formats over time while maintaining system stability [1, 2].

### **Informative Summary of Chapter 5**

*   **The Need for Evolvability**: Applications are in constant flux, and changes to features usually require changes to the data they store [2, 3]. In large systems, these changes cannot happen instantaneously, leading to the coexistence of different versions of code and data formats [4, 5].
*   **Compatibility Semantics**: 
    *   **Backward Compatibility**: New code must be able to read data written by old code [5].
    *   **Forward Compatibility**: Old code must be able to read data written by new code, typically by ignoring fields it doesn't understand [5, 6].
*   **Encoding Formats**: Programs use two representations of data: **in-memory** (objects, structs) and **byte sequences** (for files or networks) [7]. The translation between them is known as **encoding** (or serialization) and **decoding** [8].
*   **Standardized Encodings**:
    *   **JSON, XML, and CSV**: Widespread and human-readable, but have issues with data types (like distinguishing numbers from strings) and lack mandatory schemas [9, 10].
    *   **Binary Variants**: Formats like **MessagePack** offer minor space savings over JSON by using a binary representation but still include all field names in the data [11, 12].
*   **Schema-Driven Binary Formats**:
    *   **Protocol Buffers (protobuf)**: Uses a schema and numeric **field tags** to identify fields [13, 14]. Evolution is handled by adding new tags; old code ignores unknown tags while new code provides defaults for missing ones [6].
    *   **Avro**: Does not use tags; instead, it uses a **writer's schema** and a **reader's schema** [15, 16]. It resolves differences by matching field names and is particularly friendly to **dynamically generated schemas** from relational databases [17, 18].
*   **Modes of Dataflow**: Data flows between processes in several ways, each requiring careful compatibility management:
    *   **Via Databases**: Storing data for your "future self" requires backward compatibility; rolling upgrades of application instances require forward compatibility [19, 20].
    *   **Via Services (REST and RPC)**: Clients and servers communicate via APIs [21]. Unlike databases, these often involve multiple languages and require standard interface definitions [22, 23].
    *   **Via Workflows and Durable Execution**: Systems like **Temporal** provide exactly-once semantics by logging every task step to durable storage [24, 25].
    *   **Via Asynchronous Messaging**: **Message brokers** (like Kafka) or **actor frameworks** allow decoupled communication where the producer doesn't wait for the recipient [26-28].

***


​