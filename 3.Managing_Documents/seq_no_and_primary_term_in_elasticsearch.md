## 📌 **Table of Contents**  

1️⃣ **[Introduction](#1)** – `_seq_no` aur `_primary_term` ka overview.  
2️⃣ **[Scenario: E-commerce Product Update](#2)** – Real-world example ke saath explanation.  
3️⃣ **[Creating the Document](#3)** – Document insert karne par `_seq_no` aur `_primary_term` ka behavior.  
4️⃣ **[Updating the Document](#4)** – Sequence number ka increment kaise hota hai?  
5️⃣ **[When does `_primary_term` change?](#5)** – Cluster failure aur leader election ka impact.  
6️⃣ **[Summary Table](#6)**   
7️⃣ **[Importance of `_seq_no` and `_primary_term`](#7)** – Concurrency control aur data consistency.  
8️⃣ **[Conclusion](#8)** – Key takeaways aur final thoughts. 🚀  

---

# 📌 **seq_no and primary_term in Elasticsearch**  <a id="1"></a>

Elasticsearch me **`seq_no` (Sequence Number)** aur **`primary_term`** do important fields hote hain jo **concurrency control** aur **data consistency** maintain karne ke liye use hote hain.  

Chalo ek **real-world example** ke saath in dono ko samajhte hain.  

---

## **🔹 Scenario: E-commerce Product Update**  <a id="2"></a>

Maan lo ek **e-commerce app** hai aur hum ek **product "Laptop"** ka data Elasticsearch me store kar rahe hain.  

📌 **Index Name:** `products`  
📌 **Document ID:** `101`  
📌 **Data:**  
```json
{
    "name": "Laptop",
    "brand": "Dell",
    "price": 75000,
    "stock": 20
}
```

---

## **🛠 Step 1: Creating the Document**  <a id="3"></a>
Sabse pehle hum **ek new product create karte hain**:  
```http
PUT /products/_doc/101
{
    "name": "Laptop",
    "brand": "Dell",
    "price": 75000,
    "stock": 20
}
```

✅ **Response:**
```json
{
    "_index": "products",
    "_id": "101",
    "_version": 1,
    "result": "created",
    "_seq_no": 0,
    "_primary_term": 1,
    "_shards": {
        "total": 2,
        "successful": 2,
        "failed": 0
    }
}
```

🔹 **Yaha `seq_no = 0` aur `primary_term = 1` mila.**  

---

## **🛠 Step 2: Updating the Document (First Update)**  <a id="4"></a>
Ab hum `price` update karte hain:  
```http
POST /products/_update/101
{
    "doc": {
        "price": 70000
    }
}
```

✅ **Response:**
```json
{
    "_index": "products",
    "_id": "101",
    "_version": 2,
    "result": "updated",
    "_seq_no": 1,
    "_primary_term": 1
}
```

🔹 **Sequence Number (`_seq_no`) badh gaya**:  
- Pehle `seq_no = 0` tha  
- Update ke baad `seq_no = 1` ho gaya  

🔹 **Primary Term (`_primary_term`) same raha (1)**, kyunki **cluster me leader node change nahi hua**.  

---

## **🛠 Step 3: Second Update**
Agar hum fir se stock update karte hain:  
```http
POST /products/_update/101
{
    "doc": {
        "stock": 15
    }
}
```

✅ **Response:**
```json
{
    "_index": "products",
    "_id": "101",
    "_version": 3,
    "result": "updated",
    "_seq_no": 2,
    "_primary_term": 1
}
```

🔹 **Sequence Number (`_seq_no`) fir badh gaya**:  
- Pehle `seq_no = 1` tha  
- Ab `seq_no = 2` ho gaya  

🔹 **Primary Term (`_primary_term`) ab bhi same hai (`1`)**, kyunki **leader node change nahi hua**.

---

## **🔹 When does `_primary_term` change?**  <a id="5"></a>
Agar cluster me **leader node fail ho jaye** ya **naya leader elected ho**, tab **`_primary_term` change hota hai**.  

### **Example: Node Failure**
Agar **current primary node fail ho jaye** aur cluster **naya leader elect kare**, fir **`_primary_term` increase ho jata hai**.  
Maan lo **cluster fail ho gaya** aur **new leader node elect hua**, fir `primary_term = 2` ho sakta hai.

Agar **ab hum update kare**:
```http
POST /products/_update/101
{
    "doc": {
        "stock": 10
    }
}
```

✅ **Response:**
```json
{
    "_index": "products",
    "_id": "101",
    "_version": 4,
    "result": "updated",
    "_seq_no": 3,
    "_primary_term": 2
}
```
🔹 **`seq_no` update hota hai (`3`)**  
🔹 **`primary_term` bhi badh gaya (`2`)**, kyunki **leader node change ho gaya tha**.  

---

## **📌 Summary:** <a id="6"></a>
| Action        | `_seq_no` | `_primary_term` |
|--------------|---------|---------------|
| **Document Created**  | 0       | 1             |
| **First Update** (price = 70000)  | 1       | 1             |
| **Second Update** (stock = 15)  | 2       | 1             |
| **Cluster Leader Change**  | 2       | 2             |
| **Third Update** (stock = 10)  | 3       | 2             |

---

## **🔹 Why are `_seq_no` and `_primary_term` important?** <a id="7"></a>
✅ **Concurrency Control:**  
   - Elasticsearch **optimistic concurrency control** use karta hai.  
   - Agar **do clients ek hi document update kar rahe hain**, to `_seq_no` aur `_primary_term` ensure karte hain ki **latest version me update ho**.  

✅ **Data Consistency:**  
   - Agar **node fail ho jaye**, to naya leader node aata hai aur `primary_term` badh jata hai.  
   - Isse **data corruption avoid hota hai**.  

✅ **Elasticsearch's Internal Versioning System:**  
   - `_seq_no` increment hota hai **har ek successful write operation pe**.  
   - `_primary_term` badalta hai **jab primary node change hota hai**.  

---

## **🚀 Conclusion** <a id="8"></a>
👉 **`_seq_no` har update ke saath badhta hai.**  
👉 **`_primary_term` tab badhta hai jab cluster me leader node change hota hai.**  
👉 **Ye system concurrency control aur data consistency ke liye use hota hai.**  

---
[Reference Video Link](https://youtu.be/ifr0uxteQNM?si=DObdf63g6Tvj7OGH)
---