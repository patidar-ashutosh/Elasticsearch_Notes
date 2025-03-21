# 📌 Sharding & Scalability in Elasticsearch

## 📖 Table of Contents  

1️⃣ **[🔥 Introduction to Scaling in Elasticsearch](#1)**  
2️⃣ **[🧩 What is Sharding?](#2)**  
3️⃣ **[⚙️ How Sharding Works in Elasticsearch?](#3)**  
4️⃣ **[🎯 Why Sharding is Important?](#4)**  
5️⃣ **[🏗 Primary vs Replica Shards](#5)**  
6️⃣ **[⚖️ Over-Sharding & Best Practices](#6)**  
7️⃣ **[🚀 Query Performance & Sharding](#7)**  
8️⃣ **[🛠 Sharding Configuration & Management](#8)**  
9️⃣ **[🔄 Scaling Strategies & Use Cases](#9)**  
🔟 **[📌 Conclusion](#10)**  

---

## 🔥 1. Introduction to Scaling in Elasticsearch <a id="1"></a>

Elasticsearch ek **distributed, scalable** search engine hai jo **huge data** ko efficiently store aur search karne ke liye use hota hai. Agar data volume zyada ho, to **ek single node** par usko store karna possible nahi hota. Iska solution hai **Sharding**, jo Elasticsearch ko **horizontally scale** karne me madad karta hai.

---

## 🧩 2. What is Sharding? <a id="2"></a>

**Sharding** ka matlab hai ek **index ko chhote-chhote parts** me todna. Har part ko **Shard** kaha jata hai. Shards Elasticsearch ke andar **Lucene indices** hote hain, jo independently kaam kar sakte hain.

📌 **Key Points:**
- **Index level pe sharding hoti hai**, node ya cluster level pe nahi.
- Har shard ek **mini index** ki tarah hota hai.
- Elasticsearch **automatically shards** the index when created.
- Ek shard **ek single node** pe store hota hai.

---

## ⚙️ 3. How Sharding Works in Elasticsearch? <a id="3"></a>

Agar ek index me **billion documents** hain, aur ek shard usko store nahi kar sakta, to **shards me divide karna** zaroori ho jata hai.

🔹 **Example:**

Maan lo tumhare paas **2 nodes hain**, aur ek **600GB ka index** store karna hai:
- Agar ek node sirf **500GB store** kar sakti hai, to **single shard me store karna possible nahi** hai.
- Agar hum **index ko 2 shards** me tod dein, jisme har shard **300GB** ka ho, to ek shard **ek node pe** jaayega.
- Is tarah **data distribute ho jata hai** aur cluster grow kar sakta hai.

```plaintext
 Index:     |----------------------------|
 Shard 1:   |-------------|  (300GB) → Node 1
 Shard 2:   |-------------|  (300GB) → Node 2
```

📌 **Sharding ka fayda:**
- **Data loss risk kam hota hai**
- **Query performance improve hoti hai**
- **Index ko multiple nodes me store kiya ja sakta hai**

## Example
**ek index ka size 600GB hai**, aur tumhare paas **do nodes (A & B) hain, jinka individual storage 500GB** hai, to **600GB ka pura index ek node me fit nahi hoga**.  

### **🚨 Problem: Index ka size ek node se bada hai!**  
- **Node A & Node B** dono milkar **1000GB (1TB) storage** provide kar rahe hain.  
- **Index ka size 600GB hai**, jo **ek single node me store nahi ho sakta** kyunki ek node ki max storage **500GB** hai.  
- **Agar index sirf ek shard me bana**, to wo ek node me hi store hoga, lekin **shard ki max limit ek node ki limit se zyada hai**.  

➡️ **Solution:** Index ko multiple shards me todna hoga!  

## **📌 Solution: Sharding Ka Use Karke Index Ko Nodes Me Distribute Karna**  
✅ **Agar tum index ko 3 shards me divide kar do**, to har shard alag node pe store ho sakta hai.  
✅ **600GB ka data agar 3 shards me split ho to:**  
- **Shard 1 → 200GB**  
- **Shard 2 → 200GB**  
- **Shard 3 → 200GB**  

✅ **Agar Elasticsearch ko auto-allocate karne do, to:**  
| **Node** | **Shard** | **Size** |
|---------|---------|---------|
| **Node A** | Shard 1 | 200GB |
| **Node A** | Shard 2 | 200GB |
| **Node B** | Shard 3 | 200GB |
| **Total Usage** | | **600GB** (Distributed across nodes) |

🚀 **Ab pura index store ho sakta hai, kyunki shards alag-alag nodes pe distribute ho gaye!**  

---

## **📌 Sharding Ka Working Mechanism**  
✅ Jab tum **ek index create karte ho**, to **Elasticsearch us index ko multiple shards me divide karta hai**.  
✅ **Default me har index ke 1 primary shard aur 1 replica shard hota hai** (Par tum isko customize bhi kar sakte ho).  
✅ **Har shard ek chote index ki tarah kaam karta hai**, jo apne data ko alag store karta hai.  

📌 **Example**:  
Maan lo tumne **"products"** naam ka ek index banaya, jisme **10 million documents** store hone hain. Agar **Elasticsearch ek hi shard me saara data store kare, to ek node overload ho sakti hai.**  

### **➡️ Solution: Index Ko 3 Shards Me Divide Karo**  
```plaintext
Index: products
Shards: 
  - Shard 1 → (3 million documents)
  - Shard 2 → (3 million documents)
  - Shard 3 → (4 million documents)
```
✅ Ab jab tum search karoge, to Elasticsearch **parallelly** teenon shards pe query run karega, jo **fast response** dega!  

---

## 🎯 4. Why Sharding is Important? <a id="4"></a>

✅ **Horizontal Scalability:** Bina sharding ke **data store nahi ho payega** jab index bada ho.
✅ **High Availability:** Agar ek shard down ho jaye to **replica shards** backup ke liye ready rehte hain.
✅ **Parallel Processing:** Queries **multiple shards pe simultaneously execute** ho sakti hain.

---

## 🏗 5. Primary vs Replica Shards <a id="5"></a>

🔹 **Primary Shard:**
- Original shard jo **index create hone pe banta hai**
- Har index ka **at least ek primary shard hota hai**

🔹 **Replica Shard:**
- Primary shard ka **duplicate copy**
- **Data loss se bachata hai**
- **Query performance** ko improve karta hai

```plaintext
Index: Users_Data
Primary Shards:   [Shard 1]  [Shard 2]
Replica Shards:   [Shard 1']  [Shard 2']
```

---

## ⚖️ 6. Over-Sharding & Best Practices <a id="6"></a>

Agar shards **zyada ho jaye** to **performance issue** aa sakta hai. Over-sharding se bachne ke liye:
- Chhoti indices ke liye **default 1 shard hi rakho**
- Agar **millions of documents expect kar rahe ho**, to 5 shards ek **achha starting point** hai
- **Test & Monitor** karo shard allocation ko

---

## 🚀 7. Query Performance & Sharding <a id="7"></a>

**Shards distributed hone se queries parallel execute hoti hain**, jo performance ko boost karta hai.

✅ **Example:**

Agar ek **search query** ek **single shard** pe lage to time zyada lagega. Agar same query **multiple shards pe distribute** ho jaye to speed fast ho jayegi.

```json
{
  "query": {
    "match": {
      "name": "Elasticsearch"
    }
  }
}
```

🔹 Agar index **5 shards** me divided hai, to **query parallel run hogi** aur **fast results** milenge!

---

## 🛠 8. Sharding Configuration & Management <a id="8"></a>

Elasticsearch me **default shard count 1** hota hai (v7 ke baad). Agar index create karte waqt sharding set karni ho:

```json
PUT /my_index
{
  "settings": {
    "number_of_shards": 3,
    "number_of_replicas": 1
  }
}
```

👉 **Shards increase nahi ho sakti** index banne ke baad, par **Split API** ka use karke bada kiya ja sakta hai.
👉 **Shard count kam karne ke liye** Shrink API use hoti hai.

---

## 🔄 9. Scaling Strategies & Use Cases <a id="9"></a>

✅ **Scale Out (Add More Nodes):** Agar storage kam pad raha hai, to **new nodes add karo**
✅ **Increase Replica Shards:** Query load handle karne ke liye **replica shards badhao**
✅ **Use Time-Based Indices:** Agar time-series data hai to **monthly ya daily indices create karo**
✅ **ILM (Index Lifecycle Management):** Purane indices delete ya archive karne ke liye ILM policies ka use karo

---

## 📌 10. Conclusion <a id="10"></a>

- **Sharding ka main goal hai scalability aur performance**
- **Default 1 shard hota hai, but large indices ke liye 5-6 shards rakhna achha hota hai**
- **Queries parallel execute hoti hain, jo speed improve karti hai**
- **Over-sharding se bachne ke liye carefully shard count decide karo**
- **Elasticsearch me scaling horizontally hoti hai, not vertically**


