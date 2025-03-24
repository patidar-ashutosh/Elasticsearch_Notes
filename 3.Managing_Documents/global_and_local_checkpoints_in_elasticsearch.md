## 📌 **Table of Contents**  

1️⃣ **[Introduction](#1)**  
2️⃣ **[Local Checkpoint](#2)**  
3️⃣ **[Global Checkpoint](#3)**  
4️⃣ **[Step-by-Step Example](#4)**  
5️⃣ **[Why are Global & Local Checkpoints Important?](#5)**  
6️⃣ **[Conclusion](#6)**  
7️⃣ **[Final Thought](#7) 🚀**  

---

# 📌 **Global & Local Checkpoints in Elasticsearch**  <a id="1"></a>

Elasticsearch me **checkpoints** ka use **data consistency** aur **fault tolerance** maintain karne ke liye hota hai. Do important checkpoints hote hain:  

1️⃣ **Local Checkpoint**  
2️⃣ **Global Checkpoint**  

Ye dono **write operations** aur **replication process** me critical role play karte hain. Chalo inko **ek example ke saath** samajhte hain.  

---

## **🔹 1. Local Checkpoint**  <a id="2"></a>
Local checkpoint **har shard ke andar track hota hai**. Ye **last successfully processed sequence number ko represent karta hai**.  

📌 **Example:**  
Maan lo **primary shard P1** ke **sequence numbers** ye hain:  
```
(seq_no = 0, 1, 2, 3, 4, 5)
```
Agar **`seq_no = 4` tak successfully commit ho gaya hai**, to **local checkpoint = 4** hoga.  

> **Local Checkpoint:** Har individual shard ka latest successfully processed `seq_no`.  

🔹 **Ye sirf ek shard ka local state track karta hai**, **pure cluster ka nahi**.  

---

## **🔹 2. Global Checkpoint**  <a id="3"></a>
Global checkpoint **poore cluster me replicated data ka consistency ensure karta hai**. Ye **sabhi replicas ke local checkpoints ka minimum value hota hai**.  

📌 **Example:**  
Maan lo **ek index ke 1 primary aur 2 replica shards hain**:  
```
Primary (P1) -> Local Checkpoint = 5  
Replica 1 (R1) -> Local Checkpoint = 4  
Replica 2 (R2) -> Local Checkpoint = 3  
```
✅ **Global Checkpoint kya hoga?**  
Global Checkpoint = **min(P1, R1, R2) = min(5, 4, 3) = 3**  

> **Global Checkpoint:** **Sabhi shards ke local checkpoints ka minimum value**.  

🔹 **Global checkpoint ka matlab:**  
- Cluster **sirf wo writes confirm karega jo sabhi shards pe safely commit ho chuki hain**.  
- Agar **ek replica late hai**, to **global checkpoint bhi delay hoga**.  

---

## **🛠 Example: Step-by-Step Explanation** <a id="4"></a>
Maan lo **`products` index** me **documents add ho rahe hain**.  

### **1️⃣ Write Operation (Document Create)**
```http
PUT /products/_doc/1
{
    "name": "Laptop",
    "brand": "Dell",
    "price": 75000
}
```
✅ **Primary Shard (P1)** pe write ho gaya, aur **local checkpoint** badh gaya.  

### **2️⃣ Replication to Replicas**
Primary shard ke data **Replica 1 (R1)** aur **Replica 2 (R2)** me copy ho raha hai.  

```
Seq No |  Primary (P1)  |  Replica 1 (R1)  |  Replica 2 (R2)  |  Global Checkpoint
---------------------------------------------------------------------------------
  0    |    ✅ Done    |     ✅ Done     |     ✅ Done     |        0
  1    |    ✅ Done    |     ✅ Done     |     ✅ Done     |        1
  2    |    ✅ Done    |     ✅ Done     |     ✅ Done     |        2
  3    |    ✅ Done    |     ✅ Done     |     ❌ Pending  |        2
  4    |    ✅ Done    |     ✅ Done     |     ❌ Pending  |        2
  5    |    ✅ Done    |     ❌ Pending  |     ❌ Pending  |        2
```

🔹 **Yaha par** `seq_no = 3, 4, 5` abhi tak **sabhi replicas me commit nahi hua**, isiliye **global checkpoint = 2** hi hai.  

---

## **🔹 Why are Global & Local Checkpoints Important?** <a id="5"></a>
✅ **Consistency Maintain Karna:**  
   - Agar **server crash ho jaye**, to Elasticsearch **global checkpoint tak ka data safely recover** kar sakta hai.  

✅ **Read Performance Improve Karna:**  
   - Search queries **sirf global checkpoint tak ke committed data pe chalayi ja sakti hain**.  

✅ **Fault Tolerance Ensure Karna:**  
   - Agar **koi replica shard fail ho jaye**, to **global checkpoint ensure karega ki koi data loss na ho**.  

---

## **🚀 Conclusion**  <a id="6"></a>
| **Concept**           | **Definition** |
|----------------------|---------------|
| **Local Checkpoint** | Har shard ka **latest successfully written sequence number** |
| **Global Checkpoint** | Cluster ke sabhi shards ke local checkpoints ka **minimum value** |
| **Purpose** | **Consistency & Fault Tolerance** ensure karna |

---

## **🤔 Final Thought**  <a id="7"></a>
👉 **Local checkpoint sirf ek shard ke andar track hota hai.**  
👉 **Global checkpoint poore cluster ka consistency maintain karta hai.**  
👉 **Agar ek replica slow ho, to global checkpoint bhi delay hoga.**  