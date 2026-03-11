![[Screenshot 2026-03-11 at 9.23.30 PM.png]]

## Time Complexity of Consistent Hashing

The efficiency of Consistent Hashing relies heavily on its underlying data structure. To achieve optimal routing and dynamic scaling, the **Hash Ring** is typically implemented as a **balanced binary search tree** (e.g., Red-Black Tree, often using `TreeMap` in Java) or a **sorted array** (employing binary search).

Let $N$ be the total number of nodes on the hash ring. If virtual nodes are used, the total nodes can be expressed as:

$$
N = P \times V
$$

Where:
- $P$ is the number of **physical nodes**.
- $V$ is the number of **virtual nodes per physical node**.

---

### 1. Data Routing (Finding the Correct Node)

When data needs to be stored or retrieved, the system must determine the responsible node.

**Procedure:**
1. Compute the hash $H$ of the data key: $H = \text{hash}(\text{key})$. This takes $O(1)$.
2. Search the data structure for the first node whose hash $H_{\text{node}} \ge H$ (moving clockwise on the ring).

**Time Complexity:**
Searching for the successor node in a Red-Black Tree or using binary search on a sorted array takes:
$$T_{\text{routing}} = \mathcal{O}(\log N)$$

---

### 2. Node Addition (Scaling Out)

Adding a new physical server into the cluster.

**Procedure:**
1. Calculate hash values for all $V$ virtual nodes of the new physical machine.
2. Insert these $V$ hash values into the Red-Black Tree or sorted array.
3. Migrate corresponding data from the immediate successor nodes to the new node.

**Time Complexity:**
Inserting a single element into the tree/array takes $\mathcal{O}(\log N)$. For a physical node with $V$ virtual nodes, the insertion complexity is:
$$T_{\text{insertion}} = \mathcal{O}(V \log N)$$
*(Note: Data migration time depends on network bandwidth and data size, excluding algorithmic complexity).*

---

### 3. Node Removal (Scaling In / Crash Recovery)

When a physical machine goes offline.

**Procedure:**
1. Locate the $V$ hash values associated with the failed node.
2. Delete them from the tree/array.

**Time Complexity:**
Deleting a node from a Red-Black tree takes $\mathcal{O}(\log N)$. For $V$ virtual nodes, the deletion complexity is:
$$T_{\text{deletion}} = \mathcal{O}(V \log N)$$

---

### Summary: Consistent Hashing vs. Traditional Modulo

| Metric | Traditional Modulo: `hash(key) % N` | Consistent Hashing |
| :--- | :--- | :--- |
| **Routing Complexity** | $\mathcal{O}(1)$ | $\mathcal{O}(\log N)$ |
| **Rehashing Overhead** | $\mathcal{O}(M)$ — Nearly all $M$ keys must be rehashed, risking cache avalanche. | $\mathcal{O}(M/N)$ — Only impacts keys mapped to adjacent nodes. |
| **Best Use Case** | Monolithic systems with a fixed cache size. | Distributed systems requiring dynamic elasticity. |

> **Conclusion:**
> Consistent Hashing sacrifices a marginal routing delay (from $\mathcal{O}(1)$ to $\mathcal{O}(\log N)$) to gain remarkable scalability. In practice, a $\mathcal{O}(\log N)$ search over millions of nodes operates in the sub-microsecond range, making the overhead entirely negligible while strictly containing data migration costs.


### High availability[](https://www.educative.io/courses/grokking-the-system-design-interview/evaluation-of-a-distributed-caches-design#High-availability)

We have improved the availability through redundant cache servers. Redundancy adds a layer of reliability and fault tolerance to our design. We also used the [leader-follower algorithm](https://www.educative.io/courses/grokking-the-system-design-interview/data-replication#Single-leader-/-primary-secondary-replication) to conveniently manage a cluster shard. However, we haven’t achieved high availability because we have two shard replicas, and at the moment, we assume that the replicas are within a data center.

It’s possible to achieve higher availability by splitting the leader and follower servers among different data centers. But such high availability comes at a price of consistency. We assumed synchronous writes within the same data center. But synchronous writing for strong consistency in different data centers has a serious performance implication that isn’t welcomed in caching systems. We usually use asynchronous replication across data centers.

For replication within the data center, we can get strong consistency with good performance. We can compromise strong consistency across data center replication to achieve better availability (see CAP and PACELC theorems).

### Memcached vs Redis
Even though Memcached and Redis both belong to the NoSQL family, there are subtle aspects that set them apart:

| Feature | Memcached | Redis |
| :--- | :--- | :--- |
| **Simplicity & Control** | Simple, leaves cluster management to developers (finer control). | Automates most scalability and data division tasks. |
| **Persistence** | **No native support.** (Requires third-party tools). | **Yes.** Supports AOF (append-only file) and RDB (snapshots). |
| **Data Types** | **Simple Key-Value (Strings only).** | **Rich Data Structures:** Strings, lists, sets, sorted sets, hashes, bitmaps, HyperLogLog, etc. |
| **Memory Usage** | Uses **slab allocation** to reduce fragmentation, but can waste memory with small objects or varying sizes. | Highly optimized for small items. Better overall memory utilization for complex objects. |
| **Multithreading** | **Multithreaded.** Efficiently uses multi-core systems. | **Single-threaded** (core execution). Avoids complex lock/thread management. |
| **Replication & Clustering**| Relies on third-party tools. Architecturally simple to scale horizontally. | **Built-in Replication & Clustering.** Native support for Master-Slave and Redis Cluster (though setup can be complex). |
| **Primary Use Case** | Best for simple caching of large objects (e.g., >100 KB files) or HTML fragments. | Best for complex data manipulation, ranking, leaderboards, and scenarios needing persistence. |


## Features Offered by Memcached and Redis

|                            |                                |                          |
| -------------------------- | ------------------------------ | ------------------------ |
| **Feature**                | **Memcached**                  | **Redis**                |
| Low latency                | Yes                            | Yes                      |
| Persistence                | Possible via third-party tools | Multiple options         |
| Multilanguage support      | Yes                            | Yes                      |
| Data sharding              | Possible via third-party tools | Built-in solution        |
| Ease of use                | Yes                            | Yes                      |
| Multithreading support     | Yes                            | No                       |
| Support for data structure | Objects                        | Multiple data structures |
| Support for transaction    | No                             | Yes                      |
| Eviction policy            | LRU                            | Multiple algorithms      |
| Lua scripting support      | No                             | Yes                      |
| Geospatial support         | No                             | Yes                      |