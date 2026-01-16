---
title: "Why Modern Databases Use LSM Trees Instead of B-Trees"
datePublished: Fri Jan 16 2026 20:49:26 GMT+0000 (Coordinated Universal Time)
cuid: cmkhcqcrh000e02l1cg5171gh
slug: why-modern-databases-use-lsm-trees-instead-of-b-trees
tags: cassandra, dynamodb, databases, lsmtree, b-trees-and-b-trees

---

Traditional databases like MySQL and PostgreSQL use **B-trees** as their primary storage structure. B-trees modify data **in place on disk**, which means updates can happen at many different disk locations. This leads to **random disk writes**, which are expensive, especially at scale.

Because data is updated in place, these systems also rely heavily on **locks**. When multiple processes try to read or write the same record, one writer may acquire the lock while others are forced to wait until the lock is released. This waiting is known as **lock contention**. As concurrency increases, lock contention grows, causing higher latency and reduced throughput. Together, random disk writes and lock contention make traditional B-tree-based databases slower at large scale, particularly for write-heavy workloads.

To address these problems, modern systems use **LSM trees (Log-Structured Merge Trees)**, which are optimized for **massive write throughput** rather than read-heavy workloads. The key idea behind LSM trees is to **avoid in-place updates** and instead rely on **sequential I/O**. Data is written quickly first and organized later in the background. This “write now, organize later” approach makes writes extremely fast and scalable.

---

### **Core Components of an LSM Tree**

1. **Write-Ahead Log (WAL)**
    
    Every write is first appended to the WAL. This is a sequential disk write and guarantees durability. If the system crashes, the database can replay the WAL to recover lost in-memory data.
    
2. **MemTable**
    
    After the WAL, writes go into the **MemTable**, which is an in-memory sorted data structure (often a skip list or balanced tree). Recent reads are served from the MemTable, making them very fast.
    
3. **Immutable MemTable**
    
    When the MemTable reaches a size limit, it is frozen and becomes an **immutable MemTable**. A new MemTable is created for incoming writes, allowing the system to continue accepting writes without blocking. The immutable MemTable is later flushed to disk.
    
4. **SSTables (Sorted String Tables)**
    
    Immutable MemTables are flushed to disk as **SSTables**. SSTables are immutable, sorted, and written sequentially, which avoids random disk I/O.
    

---

### **Read Path in an LSM Tree**

When a read request comes in:

1. The system first checks the active MemTable.
    
2. Then it checks any immutable MemTables.
    
3. If the key is not found, it checks SSTables on disk, starting from the **newest** to the **oldest**.
    

Checking many SSTables could be expensive, so LSM trees use **Bloom filters**.

---

### **Bloom Filters**

Each SSTable has an associated **Bloom filter**, which is a probabilistic data structure that quickly tells whether a key **cannot exist** in that SSTable.

* If the Bloom filter says “no,” the SSTable is skipped entirely.
    
* If it says “maybe,” the SSTable is accessed.
    

This significantly reduces unnecessary disk reads and improves read performance.

---

### **Deletes and Tombstones**

Because LSM trees are append-only, data cannot be deleted in place. Instead, deletes are handled by writing a **tombstone**, which marks a record as deleted. When reading data, the system checks newer entries first; if it encounters a tombstone, it knows the record has been deleted.

---

### **Compaction**

Actual cleanup happens during a background process called **compaction**. During compaction:

* The database merges multiple SSTables
    
* Matches old data with their corresponding tombstones
    
* Removes both the deleted data and the tombstones
    
* Frees up disk space
    

Compaction is also responsible for keeping read performance manageable over time.

---

### **Trade-offs of LSM Trees**

* **Writes** are very fast and scalable due to sequential I/O.
    
* **Reads** are more complex and can be slower due to multiple layers.
    
* Compaction introduces overhead and must be carefully tuned.
    

Because of these characteristics, LSM trees are ideal for **write-heavy systems** such as:

* Logs
    
* Metrics
    
* Time-series data
    
* Event-driven systems
    

---

### **Real-World Systems Using LSM Trees**

Databases that use LSM trees include:

* Cassandra
    
* DynamoDB
    
* HBase
    
* ScyllaDB
    

In contrast, traditional relational databases like MySQL and PostgreSQL (which use B-trees) are generally more **read-heavy** and optimized for transactional workloads.