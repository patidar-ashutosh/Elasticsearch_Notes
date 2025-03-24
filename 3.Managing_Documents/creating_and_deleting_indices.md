# 📂 Creating & Deleting Indices in Elasticsearch  

## 📌 **Table of Contents**  

1. 🔹 [Introduction](#1)  
2. 🏗️ [Creating an Index](#2)  
   - [Understanding Shards & Replicas](#2-1)
   - [Customizing Index Settings](#2-2)
3. ❌ [Deleting an Index](#3)  
4. 🔄 [Understanding Response Output](#4)  
5. 📝 [Examples with Expected Output](#5)  
6. 🎯 [Best Practices for Production](#6)  

---  

## 🔹 **Introduction**  <a id="1"></a>
Elasticsearch me data ko store karne ke liye **indices** ka use hota hai. Index ek tarah ka **database** hota hai jo multiple **shards** me divide hota hai.  
Is section me hum **index create aur delete karna** sikhenge, saath hi **shard & replica settings** kaise configure kare ye bhi dekhenge.  

---  

## 🏗️ **Creating an Index**  <a id="2"></a>

### 🔸 **Understanding Shards & Replicas**  <a id="2-1"></a>
- **Shards:** Elasticsearch ek index ko **multiple partitions (shards)** me todta hai taaki query execution fast ho.  
- **Replica Shards:** Ye **backup copies** hote hain jo failover aur load balancing ke liye use hote hain.  
- Default Elasticsearch me:  
  - `number_of_shards = 1`  
  - `number_of_replicas = 1`  

### 🔸 **Customizing Index Settings**  <a id="2-2"></a>
Agar tumhe **default settings change karni ho** to index creation ke time JSON request body me settings specify kar sakte ho.  

🔹 **Example: Creating an Index with Custom Settings**  
```json
PUT /products
{
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 2
  }
}
```
👆 Yahan `products` naam ka ek index ban raha hai jisme **2 primary shards aur 2 replica shards** honge.  

---

## ❌ **Deleting an Index**  <a id="3"></a>
Agar koi index delete karna ho to Elasticsearch ke REST API ka use karke easily delete kar sakte ho.  

🔹 **Example: Deleting an Index**  
```json
DELETE /pages
```
👆 Ye command `pages` naam ka index delete kar degi.  

📝 **Note:**  
- Delete hone ke baad **data recover nahi ho sakta**, isliye dhyan se delete karo!  
- Kibana Console me forward slash (`/`) optional hota hai, lekin maintain karna best practice hai.  

---

## 🔄 **Understanding Response Output**  <a id="4"></a>

Jab hum index create karte hain, response me **acknowledgment keys** milti hain:  

| Key                     | Meaning |
|--------------------------|---------|
| `"acknowledged": true`  | Index successfully create ho gaya |
| `"shards_acknowledged": true` | Saare primary shards allocate ho gaye |

🔹 **Example Response (Index Creation)**  
```json
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "products"
}
```
👆 Matlab **index `products` create ho gaya aur shards bhi allocate ho gaye**.  

---

## 📝 **Examples with Expected Output**  <a id="5"></a>

### ✅ **1. Creating an Index with Default Settings**  
#### **Request:**
```json
PUT /blogs
```
#### **Response:**
```json
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "blogs"
}
```

---

### ✅ **2. Creating an Index with Custom Settings**  
#### **Request:**
```json
PUT /users
{
  "settings": {
    "number_of_shards": 3,
    "number_of_replicas": 1
  }
}
```
#### **Response:**
```json
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "users"
}
```

---

### ✅ **3. Deleting an Index**  
#### **Request:**
```json
DELETE /users
```
#### **Response:**
```json
{
  "acknowledged": true
}
```

---

## 🎯 **Best Practices for Production**  <a id="6"></a>
1️⃣ Default settings use karo agar tumhe **shard & replica configuration ka deep knowledge nahi hai**.  
2️⃣ Index delete karne se pehle **backup** lena mat bhoolna!  
3️⃣ Large-scale deployments ke liye **shard size** ka dhyan rakho, kyunki zyada shards performance impact kar sakte hain.  
4️⃣ Query load aur cluster size ke hisaab se **number of shards optimize karo**.  

---

