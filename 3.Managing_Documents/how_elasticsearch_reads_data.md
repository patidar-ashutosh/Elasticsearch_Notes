# 📌 **How Elasticsearch Reads Data**  

## 📜 **Table of Contents**  

1️⃣ **[🔹 Introduction](#1)**  
2️⃣ **[🏗️ Coordinating Node](#2)**  
3️⃣ **[🔄 Routing & Replication Group](#3)**  
4️⃣ **[🧠 Adaptive Replica Selection (ARS)](#4)**  
5️⃣ **[🚀 Read Request Execution Flow](#5)**  
6️⃣ **[🎯 Key Takeaways](#6)**  

---

## 🔹 **Introduction**  <a id="1"></a>
Elasticsearch me **data read ka process kaafi optimized hota hai**!  
Pichle topic me **Routing** ka concept dekha tha, ab **single document reading** ka pura flow samjhte hain.  

⚠️ **Important:**  
Yaha **search queries ka process cover nahi kar rahe**, sirf **single document retrieval ka process** samajh rahe hain.  

---

## 🏗️ **Coordinating Node**  <a id="2"></a>
Jab bhi **read request aati hai, to ek node us request ko receive karti hai**.  
🛠️ Is node ko **Coordinating Node** kaha jata hai.  

**Coordinating Node ka kaam:**  
✅ Request ko **process aur distribute** karna  
✅ Ye **identify karta hai ki document kaunsa shard pe store hai**  
✅ Fir **correct shard se data retrieve karna**  

---

## 🔄 **Routing & Replication Group**  <a id="3"></a>
Jab Coordinating Node ek document fetch karna chahta hai, to sabse pehle **Routing Formula** apply hota hai:  

\[
shard = hash(_routing) \mod \text{number of shards}
\]

🔹 Ye formula **determine karta hai ki kaunsa shard data store kar raha hai**.  
🔹 Par **Elasticsearch sirf primary shard se data nahi uthata!**  
🔹 Instead, **ek "Replication Group" select hota hai, jisme primary aur uske replicas hote hain**.  

✔️ **Replication Group ka benefit:**  
- **Load balancing**: Sabhi requests ek hi shard pe concentrate nahi hoti.  
- **High availability**: Agar **primary shard fail ho jaye, to replicas ka use kiya ja sakta hai**.  

---

## 🧠 **Adaptive Replica Selection (ARS)**  <a id="4"></a>
📌 **Ab sawal uthta hai, agar multiple copies available hain to Elasticsearch kaunsi shard copy choose karega?**  

✅ **Answer: Adaptive Replica Selection (ARS)**  
🔹 ARS ek **intelligent algorithm** hai jo **best performing shard copy select karta hai**.  
🔹 Ye **multiple factors ko analyze karta hai, jaise ki:**  
  ✔️ Response time history  
  ✔️ Load on the shard  
  ✔️ Latency metrics  

💡 **ARS ka fayda?**  
✔️ **Faster reads** kyunki best performing replica shard select hota hai  
✔️ **Even distribution of load** across all available shards  

---

## 🚀 **Read Request Execution Flow**  <a id="5"></a>

### **Step-by-Step Process:**  
1️⃣ **Client (Application, Kibana ya Command Line) Elasticsearch ko request bhejta hai**  
2️⃣ **Request ek Coordinating Node pe aati hai**  
3️⃣ **Coordinating Node routing formula se shard determine karta hai**  
4️⃣ **Shard primary shard ya replication group me se select hota hai (ARS ka use karke)**  
5️⃣ **Coordinating Node selected shard ko request forward karta hai**  
6️⃣ **Shard se response milta hai, jo Coordinating Node collect karta hai**  
7️⃣ **Response client ko bhej diya jata hai**  

⚡ **Example Request:**  
```json
GET /products/_doc/105
```
💡 **Elasticsearch kya karega?**  
✔️ **Shard identify karega (Routing Formula use karke)**  
✔️ **Adaptive Replica Selection ka use karke best shard choose karega**  
✔️ **Document fetch karke client ko return karega**  

---

### **🔥 Bonus: Diagrammatic Representation**
```
Client ---> Coordinating Node ---> Finds Shard Using Routing ---> ARS Selects Fastest Shard (Primary/Replica) ---> Fetches Data ---> Returns to Client
```

---

### **✨ Important Points**
✅ **GET request sirf 1 shard pe jati hai, saare shards pe nahi.**  
✅ **Adaptive Replica Selection (ARS)** optimize karta hai ki kaha se data milega.  
✅ **Single document fetch bahut fast hota hai**, kyunki sirf **ek hi shard** involve hota hai.  

---

## 🎯 **Key Takeaways**  <a id="6"></a>
✅ **Coordinating Node har read request ko process karta hai**  
✅ **Routing formula use hota hai shard identify karne ke liye**  
✅ **Elasticsearch sirf primary shard pe rely nahi karta, balki replication group ka use karta hai**  
✅ **Best performing shard Adaptive Replica Selection (ARS) ke through choose hota hai**  
✅ **Ye process fast aur efficient hota hai, jo Elasticsearch ko highly scalable banata hai**  

---
[Reference Video Link](https://youtu.be/oz9PecSxE74?si=rQkFyvgPNj5r8a-9)
---