# 🔍 Retrieving Documents by ID in Elasticsearch  

## 📌 **Table of Contents**  

1. 🔹 [Introduction](#introduction)  
2. 📥 [Retrieving a Document by ID](#retrieving-a-document-by-id)  
3. 🔄 [Understanding the Response](#understanding-the-response)  
   - **_source**: Document Data  
   - **found**: Document Existence  
4. ❌ [Handling Missing Documents](#handling-missing-documents)  
5. 📝 [Examples with Expected Output](#examples-with-expected-output)  
6. 🎯 [Best Practices](#best-practices)  

---

## 🔹 **Introduction**  
Elasticsearch me documents ka access **ID ke basis par** bhi kiya ja sakta hai. Agar tumhe kisi document ka exact `_id` pata ho, to tum **GET request** ka use karke us document ko easily retrieve kar sakte ho.  

Aaj hum sikhenge **documents ko ID se retrieve karna**, **response ko samajhna**, aur **missing documents handle karna**.  

---

## 📥 **Retrieving a Document by ID**  

Agar tumhe kisi document ka `_id` pata ho, to tum **GET method** ka use karke us document ko retrieve kar sakte ho.  

🔹 **Syntax:**  
```json
GET /index_name/_doc/document_id
```

### ✅ **Example:** (Retrieve Document with `_id = 100`)  
```json
GET /products/_doc/100
```
👆 Yahan `products` index ke andar `_id = 100` wale document ko retrieve kar rahe hain.  

---

## 🔄 **Understanding the Response**  

Jab tum **GET request** bhejte ho, to response me kuch important cheezein hoti hain:  

### 🔹 **1. `_source` Key: Document Data**  
Agar document mil gaya, to response me **`_source` key ke andar pura document JSON format me milega**.  

#### **Example Response (Document Found)**  
```json
{
  "_index": "products",
  "_id": "100",
  "found": true,
  "_source": {
    "name": "Smartphone",
    "price": 25000,
    "in_stock": false
  }
}
```
👆 Yahan **`_source` ke andar pura document** aagaya jo humne store kiya tha.  

---

### 🔹 **2. `found` Key: Document Existence**  
Agar document exist nahi karta, to `found: false` return hota hai aur `_source` key response me nahi aati.  

#### **Example Response (Document Not Found)**  
```json
{
  "_index": "products",
  "_id": "999",
  "found": false
}
```
👆 Yahan `_id = 999` ka document exist nahi karta, isliye `found: false` hai.  

---

## ❌ **Handling Missing Documents**  

Agar document exist nahi karta, to Elasticsearch **404 Not Found** error return karta hai.  

🔹 **Example:**  
```json
GET /products/_doc/999
```
#### **Response:**
```json
{
  "_index": "products",
  "_id": "999",
  "found": false
}
```
📝 **Solution:**  
- Pehle check karo ki document `_id` sahi hai ya nahi.  
- Agar unsure ho, to **search query ka use karo** (jo hum aage dekhenge).  

---

## 📝 **Examples with Expected Output**  

### ✅ **1. Retrieve an Existing Document**  
#### **Request:**
```json
GET /products/_doc/100
```
#### **Response:**
```json
{
  "_index": "products",
  "_id": "100",
  "found": true,
  "_source": {
    "name": "Smartphone",
    "price": 25000,
    "in_stock": false
  }
}
```

---

### ✅ **2. Retrieve a Non-Existent Document**  
#### **Request:**
```json
GET /products/_doc/999
```
#### **Response:**
```json
{
  "_index": "products",
  "_id": "999",
  "found": false
}
```

---

## 🎯 **Best Practices**  

✔️ **Document `_id` ka dhyan rakho, kyunki Elasticsearch auto-generate karta hai agar manually specify na kiya ho**  
✔️ **Agar unsure ho document `_id` ke baare me, to search queries ka use karo**  
✔️ **404 errors handle karne ke liye error handling logic implement karo**  

---

