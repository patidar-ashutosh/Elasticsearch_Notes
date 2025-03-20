# 📌 **How Elasticsearch Writes Data**  

## 📜 **Table of Contents**  
1. 🔹 [Introduction](#introduction)  
2. 🏗️ [Write Request Flow](#write-request-flow)  
3. 🔄 [Handling Failures in Data Replication](#handling-failures-in-data-replication)  
4. 🧠 [Primary Terms & Sequence Numbers](#primary-terms--sequence-numbers)  
5. 🚀 [Global & Local Checkpoints](#global--local-checkpoints)  
6. 🎯 [Key Takeaways](#key-takeaways)  

---

## 🔹 **Introduction**  
Ab humne **Elasticsearch ka read process samajh liya**, to next step hai **write process** ka deep dive lena.  

⚠️ **Important:**  
- Write request bhi **routing process se guzar kar replication group tak pahuchti hai**  
- **Write operations sirf primary shard pe perform hoti hain**  
- **Primary shard update hone ke baad replicas ko sync kiya jata hai**  

---

## 🏗️ **Write Request Flow**  
Elasticsearch me **write request ka execution flow** kuch is tarah hota hai:

1️⃣ **Client (Application, Kibana ya Command Line) Elasticsearch ko write request bhejta hai**  
2️⃣ **Coordinating Node request ko receive karta hai aur Routing Formula apply karta hai:**  

\[
shard = hash(_routing) \mod \text{number of shards}
\]

3️⃣ **Request correct replication group ko forward hoti hai, jisme ek primary aur multiple replicas hote hain**  
4️⃣ **Write request sirf primary shard pe jaati hai (replica pe nahi)**  
5️⃣ **Primary shard pe validation hoti hai:**  
   ✔️ **Request ka structure check hota hai**  
   ✔️ **Fields ka data type validate hota hai**  
   ✔️ **Agar validation fail ho to error return hoti hai**  
6️⃣ **Primary shard successfully write operation complete karta hai**  
7️⃣ **Primary shard asynchronously replicas ko update karta hai**  
8️⃣ **Response client ko return hota hai**  

---

## 🔄 **Handling Failures in Data Replication**  
📌 **Elasticsearch distributed system hai, isliye failure handling critical hoti hai!**  
Agar **write request ke beech me primary shard fail ho jaye**, to data inconsistency ho sakti hai.  

**Example Scenario:**  
- **New document index hota hai aur primary shard successful write kar leta hai**  
- **Primary shard replica shards ko update karta hai**  
- **Agar primary shard failure ho jaye aur ek replica update na ho paye, to problem ho sakti hai**  
- **Elasticsearch ek new primary shard promote karega, par ek replica out-of-sync ho sakta hai**  

💡 **Solution:**  
Elasticsearch **Primary Terms aur Sequence Numbers** ka use karta hai taaki consistency maintain ho.  

---

## 🧠 **Primary Terms & Sequence Numbers**  
✅ **Primary Terms:**  
- Jab bhi **primary shard change hota hai, ek primary term counter increment hota hai**  
- Primary term **track karta hai ki kitni baar primary shard change ho chuka hai**  
- Cluster state me **persist hota hai**  

✅ **Sequence Numbers:**  
- **Har write operation ka ek unique sequence number hota hai**  
- **Primary shard sequence number maintain karta hai**  
- **Agar shard fail ho jaye to sequence number ka use karke missing operations identify kiye jate hain**  

🔹 **Example:**  
```json
{
  "_index": "products",
  "_id": "105",
  "_version": 3,
  "result": "updated",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 1
  },
  "_seq_no": 45,
  "_primary_term": 2
}
```
**Is response me:**  
- `_seq_no: 45` → Ye 45th operation hai  
- `_primary_term: 2` → Ye primary shard 2nd version hai  

---

## 🚀 **Global & Local Checkpoints**  
Elasticsearch me **data recovery fast karne ke liye checkpoints ka use hota hai**.  

📌 **Checkpoints ka kaam:**  
- **Data synchronization optimize karna**  
- **Agar shard fail ho jaye to sirf missing operations replay karna**  

✅ **Global Checkpoint:**  
- **Ek sequence number hota hai jo har shard pe sync hota hai**  
- **Yeh dikhata hai ki last consistent state kya thi**  
- **Jitne bhi sequence numbers iske neeche hain, wo sabhi shards pe apply ho chuke hain**  

✅ **Local Checkpoint:**  
- **Har replica shard apni ek local checkpoint maintain karta hai**  
- **Ye batata hai ki last successfully applied sequence number kya hai**  

🔹 **Agar koi replica fail ho jaye, to Elasticsearch sirf uske missing sequence numbers apply karega, puri history compare nahi karega!**  

---

## 🎯 **Key Takeaways**  
✅ **Write requests sirf primary shard pe hoti hain, replicas ko parallel update kiya jata hai**  
✅ **Agar primary shard fail ho jaye, to Elasticsearch ek replica ko new primary bana deta hai**  
✅ **Primary Terms aur Sequence Numbers ka use consistency maintain karne ke liye hota hai**  
✅ **Global aur Local Checkpoints shard recovery process ko optimize karte hain**  
✅ **Data replication asynchronous hoti hai, par consistency maintain karne ke liye extra mechanisms hain**  

---

---
[Reference Video Link](https://youtu.be/Zei4mlPatpY?si=z6ZTi3dpVBuuKi3K)
---