# 🧠 CAP Theorem — Explained Simply

The **CAP Theorem** (also known as **Brewer’s Theorem**) describes the **three fundamental trade-offs** in distributed database systems.  
It states that **a distributed system can provide only *two* of the following three guarantees at the same time**:

| **Property** | **Meaning** | **Example Behavior** |
|---------------|-------------|----------------------|
| **C — Consistency** | Every read receives the most recent write or an error. | If you update a user’s email on one node, all nodes show the new email immediately. |
| **A — Availability** | Every request receives a (non-error) response — without guarantee that it’s the latest data. | Even if one node is down, others still respond, possibly with stale data. |
| **P — Partition Tolerance** | The system continues to operate despite network failures or node partitions. | Even if some nodes can’t talk to others, the system still functions. |

---

## ⚖️ The Core Rule

> A distributed database can **only achieve two** of these three guarantees simultaneously:
> **Consistency**, **Availability**, and **Partition Tolerance**.

| **Combination** | **Meaning** | **Example Databases / Systems** |
|------------------|-------------|--------------------------------|
| **CP (Consistency + Partition Tolerance)** | Prioritizes consistency over availability — if a network partition happens, the system sacrifices availability to ensure all data remains consistent. | HBase, MongoDB (in some modes), Zookeeper, etcd |
| **AP (Availability + Partition Tolerance)** | Prioritizes uptime — even if data is stale or inconsistent, the system remains available. | Cassandra, DynamoDB, Couchbase, Riak |
| **CA (Consistency + Availability)** | Provides both only when there are **no partitions** (i.e., in a single-node or perfectly reliable network). | Traditional single-node RDBMS (PostgreSQL, MySQL, Oracle) |

---

## 🧩 How It Works — Scenario Example

Let’s say you have two database nodes: **Node A** and **Node B**.

### 🕸️ Network Partition Happens
Node A and Node B can’t communicate due to a network issue.

Now, a user sends a **write** request to Node A and a **read** request to Node B.

- If the system **prioritizes Consistency**, Node B will **reject or delay the read** until it syncs with Node A → sacrificing **Availability**.  
- If the system **prioritizes Availability**, Node B will **return possibly outdated data** → sacrificing **Consistency**.  
- In both cases, **Partition Tolerance** must exist because the network is broken.

---

## 💡 In Real World

- **Distributed databases must tolerate partitions (P)** → network issues are inevitable.  
- So in practice, they **choose between C and A**:
  - **CP systems** (strongly consistent) — suitable for **banking, inventory, financial transactions**.  
  - **AP systems** (highly available) — suitable for **social media, IoT, large-scale analytics**.

---

## ⚙️ Visual Summary (GitHub-Compatible)

```mermaid
graph TD
    CAP[CAP Theorem]
    C[Consistency]
    A[Availability]
    P[Partition Tolerance]
    CAP --> C
    CAP --> A
    CAP --> P

    CP[CP Systems (Consistency + Partition Tolerance)]
    AP[AP Systems (Availability + Partition Tolerance)]
    CA[CA Systems (Consistency + Availability)]

    C --> CP
    P --> CP
    A --> AP
    P --> AP
    C --> CA
    A --> CA
```

---

## 🧮 Summary Table

| **Category** | **Guarantees** | **Trade-off** | **Example DBs** | **Ideal Use Case** |
|---------------|----------------|----------------|------------------|--------------------|
| **CP** | Consistency + Partition Tolerance | May reject requests when partitioned (lower availability). | HBase, MongoDB (strong consistency), Zookeeper, etcd | Banking, inventory management, critical transactions |
| **AP** | Availability + Partition Tolerance | May serve stale data (eventual consistency). | Cassandra, DynamoDB, Couchbase, Riak | Social apps, IoT, event logging |
| **CA** | Consistency + Availability | Only possible without partitions (single-node). | PostgreSQL, MySQL, Oracle | Non-distributed systems, local applications |

---

## 🧭 Extension — **PACELC Theorem**

CAP doesn’t account for latency when the system *isn’t* partitioned.  
That’s where **PACELC** comes in:

> **If Partition (P) occurs → choose between Availability (A) and Consistency (C)**  
> **Else (E)** → choose between **Latency (L)** and **Consistency (C)**

| **Condition** | **Choice** |
|----------------|-------------|
| When Partition occurs (P) | Pick A (Availability) or C (Consistency) |
| When no Partition (E) | Pick L (Low Latency) or C (Consistency) |

🧩 Example:
- **DynamoDB** → **PA/EL** (Available during partitions, low latency otherwise).  
- **Spanner / YugabyteDB** → **PC/EC** (Consistent during and after partitions).
