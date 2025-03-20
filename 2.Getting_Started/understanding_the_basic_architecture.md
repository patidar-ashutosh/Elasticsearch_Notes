## 📜 Table of Contents  

1️⃣ **Introduction** – Elasticsearch Architecture Overview 🏗️  
2️⃣ **Nodes and Clusters** – How Elasticsearch Organizes Data 🔄  
3️⃣ **Documents and Indices** – Storing and Structuring Data 📄  
4️⃣ **Scalability and Availability** – Ensuring Performance and Redundancy ⚡  
5️⃣ **Practical Example** – Storing a Document in Elasticsearch 🛠️  
6️⃣ **Key Takeaways** – Summary of Core Concepts ✅  

---  

## 1️⃣ **Introduction – Elasticsearch Architecture Overview 🏗️**  

Elasticsearch ek **distributed, scalable, and real-time** search engine hai. Iska architecture highly optimized hota hai **Big Data** handle karne ke liye.  
Ab hum dekhenge ki Elasticsearch **data ko organize kaise karta hai** aur **scalable search engine** kaise banta hai.  

**3 Major Components:**  
🔹 **Node** – Running instance of Elasticsearch.  
🔹 **Cluster** – Multiple nodes ka collection.  
🔹 **Index** – Documents ka logical grouping.  

---  

## 2️⃣ **Nodes and Clusters – How Elasticsearch Organizes Data 🔄**  

### **🔹 Node kya hota hai?**  
Jab tumne Elasticsearch start kiya, to **ek node start hua**.  
📌 **Node = Elasticsearch ka ek running instance** (Ek independent process jo data store & process karta hai).  
📌 Ek machine pe multiple nodes chal sakte hain.  

🖥️ **Example:**  
Agar tumhare paas **1 physical machine** hai, to tum uspe **multiple nodes** run kar sakte ho.  
```plaintext
Machine A → Node 1, Node 2, Node 3
Machine B → Node 4, Node 5
```
Elasticsearch automatically decide karta hai ki **data ko kaise distribute karna hai**.  

### **🔹 Cluster kya hota hai?**  
**Cluster = Multiple nodes ka collection** jo ek saath milke kaam karte hain.  
📌 **Ek cluster me ek ya multiple nodes ho sakte hain**.  
📌 Agar ek node fail ho jaye, to **data loss na ho** iske liye cluster redundancy maintain karta hai.  

🖥️ **Example:**  
Agar tumhare paas **3-node cluster** hai, to har node data ka ek **part store karega**.  

```plaintext
Cluster A → Node 1 (Data Part 1), Node 2 (Data Part 2), Node 3 (Data Part 3)
```
✅ **Cluster automatically manage karega ki data kahaan store hoga**.  

🔴 **Development vs Production**  
- Development me **single node cluster** theek hai.  
- Production me **multi-node cluster** zaroori hota hai for **high availability & scalability**.  

---

## 3️⃣ **Documents and Indices – Storing and Structuring Data 📄**  

### **🔹 Document kya hota hai?**  
**Document = JSON Object** jo Elasticsearch me store hota hai.  
Har document ek **unique entity** ko represent karta hai.  

🖥️ **Example – Storing a Person’s Data**  
```json
{
  "name": "Amit Sharma",
  "country": "India"
}
```
📌 **Documents JSON format me store hote hain**.  
📌 Documents ko **search, filter, and analyze** kiya ja sakta hai.  

### **🔹 Index kya hota hai?**  
**Index = Similar documents ka collection**  
Har document ek **index ke andar store hota hai**.  

🖥️ **Example – Indexing People Data**  
```plaintext
Index: "people"
  ├── Document 1: { "name": "Amit", "country": "India" }
  ├── Document 2: { "name": "John", "country": "USA" }
  ├── Document 3: { "name": "Alice", "country": "UK" }
```
📌 Indices help karte hain **fast search & efficient storage** me.  
📌 Har index ka **apna configuration hota hai** jo scalability & availability ko define karta hai.  

---

## 4️⃣ **Scalability and Availability – Ensuring Performance and Redundancy ⚡**  

### **🔹 Scalability**  
Elasticsearch horizontally scale hota hai.  
📌 Agar tumhe zyada data store karna hai, to bas **naye nodes add kar do**.  

🖥️ **Example:**  
```plaintext
Cluster Start → Node 1 (100GB data store)
Scaling Up → Add Node 2 → Now 200GB capacity
Scaling Up → Add Node 3 → Now 300GB capacity
```
✅ **No downtime, just add more nodes!**  

### **🔹 High Availability (HA)**  
Har data ka **backup (replica shard)** automatically banaya jata hai.  
Agar ek node fail ho jaye, to data **automatically recover** ho jata hai.  

---

## Elasticsearch ka data storage flow:

1️⃣ **Data ➝ Document** me store hota hai  
2️⃣ **Document ➝ Index** ke andar store hota hai  
3️⃣ **Index ➝ Node** ke andar store hota hai  
4️⃣ **Node ➝ Cluster** ka part hota hai  

🔹 **Simplified Flow:**  
```
Data → Document → Index → Node → Cluster
```

---

## **📌 Final Structure**
```
Cluster  
 ├── Node-1  
 │    ├── Index: products  
 │    │    ├── Document ID: 1 → { "name": "Laptop", "price": 50000 }  
 │    │    ├── Document ID: 2 → { "name": "Mobile", "price": 20000 }  
 │    │  
 │    ├── Index: orders  
 │         ├── Document ID: 1 → { "order_id": 101, "amount": 1500 }  
 │  
 ├── Node-2  
 │    ├── Index: products (Replica Shard)
```

---

✅ **Cluster ke andar multiple nodes hote hain.**  
✅ **Node ke andar multiple indexes store ho sakte hain.**  
✅ **Index ke andar multiple documents store hote hain.**  

---

## 5️⃣ **Practical Example – Storing a Document in Elasticsearch 🛠️**  

Ek **"people" index** banayenge aur ek person ka document store karenge.  

### **📌 Step 1 – Create an Index**
```bash
PUT /people
```
🔹 **"people" index create ho gaya.**  

### **📌 Step 2 – Add a Document**  
```bash
POST /people/_doc/1
{
  "name": "Amit Sharma",
  "country": "India"
}
```
🔹 **Document successfully store ho gaya!**  

### **📌 Step 3 – Retrieve the Document**
```bash
GET /people/_doc/1
```
✅ **Expected Output:**
```json
{
  "_index": "people",
  "_id": "1",
  "_source": {
    "name": "Amit Sharma",
    "country": "India"
  }
}
```
📌 **"_source" field** me tumhara original data hoga.  

---

## 6️⃣ **Key Takeaways – Summary of Core Concepts ✅**  

✔️ **Elasticsearch ka architecture highly scalable aur distributed hota hai.**  
✔️ **Node = Running instance of Elasticsearch** jo data store karta hai.  
✔️ **Cluster = Multiple nodes ka collection** jo milke kaam karta hai.  
✔️ **Document = JSON object** jo Elasticsearch me store hota hai.  
✔️ **Index = Similar documents ka collection** jo efficient search allow karta hai.  
✔️ **Scalability & High Availability** ensure karne ke liye multi-node clusters use hote hain.  

---

## 🎯 **Bonus Insights – Tumhare Video Caption Me Jo Nahi Tha**  
🔥 **Elasticsearch me Sharding ka concept bhi hota hai!**  
✅ **Data multiple shards me split hota hai** jo alag-alag nodes me distribute hota hai.  
✅ **Replica shards** ensure karte hain ki agar ek shard fail ho jaye, to doosra available rahe.  

