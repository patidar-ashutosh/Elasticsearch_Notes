# Understanding Replication in Elasticsearch

## **📌 Table of Contents**  

1️⃣ **[Introduction](#1)**  
2️⃣ **[Replication Kya Hai?](#2)**  
3️⃣ **[Primary vs Replica Shard](#3)**  
4️⃣ **[Replication Kaise Kaam Karta Hai?](#4)**  
5️⃣ **[Cluster State aur Replica Allocation](#5)**  
6️⃣ **[Failover & Data Availability](#6)**  
7️⃣ **[Replication Ka Performance Pe Impact](#7)**  
8️⃣ **[Practical Example - Index Create Karna](#8)**  
9️⃣ **[Replication vs Snapshots](#9)**  
🔟 **[Conclusion](#10)**  

---

## 1️⃣ Introduction <a id="1"></a>
Replication Elasticsearch ka ek important feature hai jo data loss aur availability ko ensure karta hai. Iska main purpose hai failure tolerance aur performance improvement. Pehle humne sharding ke baare mein padha tha, jisme humne dekha ki ek index multiple shards mein split hota hai. Lekin agar koi shard jis node pe stored hai wo down ho jaye, toh data loss ho sakta hai. Isi problem ko solve karne ke liye Elasticsearch mein replication ka concept aata hai.

## 2️⃣ Replication Kya Hai? <a id="2"></a>
Replication ka matlab hai har primary shard ka ek ya ek se zyada copies (replica shards) banana. Ye replica shards independent hote hain aur kisi bhi primary shard ki tarah queries handle kar sakte hain. Default mein, Elasticsearch har shard ka ek replica shard create karta hai, lekin hum iski count badha bhi sakte hain.

### 3️⃣ ✅ Primary vs Replica Shard <a id="3"></a>
| **Shard Type** | **Role** |
|--------------|---------|
| **Primary Shard** | Original data store karta hai aur write operations handle karta hai |
| **Replica Shard** | Primary shard ki copy hoti hai, read requests handle karta hai aur failover support deta hai |

> **Replication Group**: Ek primary shard aur uske saare replica shards ko milakar ek replication group kehte hain.

## 4️⃣ Replication Kaise Kaam Karta Hai? <a id="4"></a>
- Jab hum ek index create karte hain, toh hum uske shards aur replicas define kar sakte hain.
- Primary shard par jo bhi data likha jata hai, wo automatically uske replica shards par bhi copy ho jata hai.
- Replica shards **same node par store nahi hote** jahan primary shard hai, taaki agar ek node fail ho jaye toh data accessible rahe.

## 5️⃣ Cluster State aur Replica Allocation <a id="5"></a>
### **Single Node Cluster (No Effect of Replication)**
Agar cluster mein sirf ek node ho, toh replica shards "UNASSIGNED" state mein rahenge kyunki unke liye allocate hone ke liye doosri node available nahi hai.

### **Multi-Node Cluster (Replication Enabled)**
Jaise hi ek naya node cluster mein add hota hai, Elasticsearch automatically replica shards ko naye node par assign kar deta hai. Iss tarike se agar koi node fail bhi ho jaye, toh data lost nahi hota aur cluster ka health "green" rahta hai.

## 6️⃣ Failover & Data Availability <a id="6"></a>
Agar kisi wajah se primary shard unavailable ho jata hai (jaise disk failure ya node down ho jaye), toh Elasticsearch kisi ek replica shard ko **primary shard promote** kar deta hai.

### **Failover Process Steps:**
1. Primary shard fail hota hai 🚨
2. Elasticsearch automatically ek available replica ko primary promote karta hai 🔄
3. Naya replica assign hota hai taaki cluster balanced rahe ✅

## 7️⃣ Replication Ka Performance Pe Impact <a id="7"></a>
Replication sirf failover ke liye nahi, balki query performance improve karne ke liye bhi useful hai. Kyunki replica shards ek full copy hote hain, Elasticsearch inhe **read operations** ke liye use karta hai.

### **Example:**
Agar ek index ke paas 1 primary shard aur 2 replica shards hain, toh ek time par 3 concurrent read queries handle ki ja sakti hain.

## 8️⃣ Practical Example - Index Create Karna <a id="8"></a>
Elasticsearch mein ek index create karne ka basic syntax:
```bash
PUT /my_index
{
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 1
  }
}
```

### **Cluster Health Check**
```bash
GET _cluster/health
```
Is command se hum cluster ka health status check kar sakte hain:
- **Green** 🟢: Sab kuch sahi hai
- **Yellow** 🟡: Kuch replica shards unassigned hain (usually single-node cluster mein hota hai)
- **Red** 🔴: Primary shards unavailable hain, data loss ho sakta hai

## 9️⃣ Replication vs Snapshots <a id="9"></a>
Replication sirf "live data" ka backup hota hai, lekin agar data corruption ho jaye ya galti se delete ho jaye, toh replication help nahi karega. Iske liye **snapshots** use kiye jate hain jo purane states ko restore karne ka option dete hain.

| **Feature** | **Replication** | **Snapshots** |
|------------|---------------|--------------|
| **Purpose** | Failover aur Read Performance | Data Backup & Restore |
| **Data Coverage** | Sirf latest live data | Past states bhi store karta hai |
| **Usage** | Hardware failure se bachne ke liye | Data corruption ya accidental deletion ke liye |

## 🔟 Conclusion <a id="10"></a>
Replication Elasticsearch ka ek powerful feature hai jo data availability aur performance dono ensure karta hai. Production setup mein kam se kam **2 nodes** aur ek replica zaroor configure karna chahiye taaki failover possible ho. Critical systems ke liye **2 ya zyada replicas** recommended hain.


