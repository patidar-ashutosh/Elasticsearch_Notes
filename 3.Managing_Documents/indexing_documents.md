# 🗂️ Indexing Documents in Elasticsearch  

## 📌 **Table of Contents**  

1. 🔹 [Introduction](#introduction)  
2. 📤 [Indexing a Document](#indexing-a-document)  
   - Using **POST** (Auto-generated ID)  
   - Using **PUT** (Custom ID)  
3. 🔄 [Understanding the Response](#understanding-the-response)  
   - **_shards**: Document Distribution  
   - **_id**: Document Identifier  
4. ⚙️ [Auto-Index Creation](#auto-index-creation)  
5. 📝 [Examples with Expected Output](#examples-with-expected-output)  
6. 🎯 [Best Practices](#best-practices)  

---  

## 🔹 **Introduction**  
Ek **index** tabhi useful hota hai jab usme **documents** stored ho. Elasticsearch me **data ko store karne ke process ko "indexing" kehte hain**. Har document **JSON format** me hota hai aur **index ke andar store** hota hai.  

Aaj hum sikhenge ki **POST** aur **PUT** methods ka use karke documents kaise index karein.  

---  

## 📤 **Indexing a Document**  

### ✅ **1. Using POST (Auto-generated ID)**  
Agar tum **document ka ID specify nahi karoge**, to Elasticsearch automatically ek unique `_id` assign karega.  

🔹 **Example:**  
```json
POST /products/_doc
{
  "name": "Laptop",
  "price": 50000,
  "in_stock": true
}
```
👆 Yahan `products` index me ek **Laptop** naam ka product store ho raha hai. `_id` automatically generate hoga.  

---

### ✅ **2. Using PUT (Custom ID)**  
Agar tum khud ek **specific `_id` assign** karna chahte ho, to tum **PUT method** ka use kar sakte ho.  

🔹 **Example:**  
```json
PUT /products/_doc/100
{
  "name": "Smartphone",
  "price": 25000,
  "in_stock": false
}
```
👆 Yahan document ka `_id = 100` set ho raha hai.  

---

## 🔄 **Understanding the Response**  

Jab hum document index karte hain, to response me **2 important cheezein** aati hain:  

### 🔹 **1. `_shards` Object: Document Distribution**  
Elasticsearch me document ek **primary shard** pe store hota hai aur fir **replica shards** pe copy hota hai.  

#### **Example Response:**  
```json
{
  "_shards": {
    "total": 3,
    "successful": 3,
    "failed": 0
  }
}
```
- **Total = 3** (1 primary + 2 replicas)  
- **Successful = 3** (Document successfully store ho gaya)  
- **Failed = 0** (Koi error nahi aayi)  

---

### 🔹 **2. `_id` Key: Document Identifier**  
- Agar **POST request** se document add karte ho, to `_id` automatically generate hota hai.  
- Agar **PUT request** se document add karte ho, to `_id` manually set hota hai.  

#### **Example Response (Auto-generated ID)**  
```json
{
  "_index": "products",
  "_id": "a1b2c3d4",
  "result": "created"
}
```
👆 Yahan Elasticsearch ne `"a1b2c3d4"` naam ka ID generate kiya hai.  

---

## ⚙️ **Auto-Index Creation**  

Elasticsearch me ek **setting `action.auto_create_index` hoti hai**, jo default `"true"` hoti hai.  
Iska matlab agar **index exist nahi karta aur tum document insert karne ki koshish karte ho**, to **Elasticsearch automatically index create kar dega**.  

🔹 **Example:**  
```json
POST /orders/_doc
{
  "customer": "Ramesh",
  "amount": 1500
}
```
Agar **`orders` index exist nahi karta**, to Elasticsearch **pehle index banayega, fir document insert karega**.  

📝 **Production me best practice yahi hai ki pehle index manually create karein!**  

---

## 📝 **Examples with Expected Output**  

### ✅ **1. Indexing a Document with Auto ID**  
#### **Request:**
```json
POST /products/_doc
{
  "name": "Tablet",
  "price": 30000,
  "in_stock": true
}
```
#### **Response:**
```json
{
  "_index": "products",
  "_id": "r4t5u6v7",
  "result": "created"
}
```

---

### ✅ **2. Indexing a Document with Custom ID**  
#### **Request:**
```json
PUT /products/_doc/200
{
  "name": "Headphones",
  "price": 5000,
  "in_stock": true
}
```
#### **Response:**
```json
{
  "_index": "products",
  "_id": "200",
  "result": "created"
}
```

---

### ✅ **3. Auto Index Creation Example**  
#### **Request (Index does not exist):**
```json
POST /employees/_doc
{
  "name": "Amit",
  "role": "Engineer"
}
```
#### **Response (Index automatically created):**
```json
{
  "_index": "employees",
  "_id": "x7y8z9w0",
  "result": "created"
}
```

---

## 🎯 **Best Practices**  

✔️ **Production me hamesha manually index create karo**  
✔️ **Agar document ka `_id` important hai, to manually specify karo (`PUT`)**  
✔️ **`action.auto_create_index` ka behavior samjho, taki unwanted indices create na ho**  
✔️ **Shards aur Replicas ka structure samjh lo, taki indexing aur searching optimized ho**  

---

