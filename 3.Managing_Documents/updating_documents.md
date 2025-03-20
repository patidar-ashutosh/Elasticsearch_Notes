# ✏️ Updating Documents in Elasticsearch  

## 📌 **Table of Contents**  

1. 🔹 [Introduction](#introduction)  
2. 🔄 [Updating an Existing Field](#updating-an-existing-field)  
3. ➕ [Adding a New Field](#adding-a-new-field)  
4. 🛠️ [How Updates Work Internally](#how-updates-work-internally)  
5. 📝 [Examples with Expected Output](#examples-with-expected-output)  
6. 🎯 [Best Practices](#best-practices)  

---

## 🔹 **Introduction**  
Ab tak humne **documents ko index** aur **retrieve** karna sikha.  
Ab hum sikhenge **documents ko update kaise karein**, chahe kisi **existing field ka value change** karna ho ya **new field add karni ho**.  

Elasticsearch me **documents immutable** hote hain, iska matlab **direct update possible nahi hota**. Jab bhi koi document update hota hai, actually **pura document replace hota hai**.  

---

## 🔄 **Updating an Existing Field**  

Agar tum kisi **document ke kisi field ka value update** karna chahte ho, to **Update API** ka use kar sakte ho.  

🔹 **Syntax:**  
```json
POST /index_name/_update/document_id
{
  "doc": {
    "field_name": "new_value"
  }
}
```

---

### ✅ **Example: Update `in_stock` Field**  
Maan lo **product sell ho gaya**, to hume **"in_stock" field ka value 4 se 3 karna hai**.  

#### **Request:**
```json
POST /products/_update/100
{
  "doc": {
    "in_stock": 3
  }
}
```
  
#### **Response:**
```json
{
  "_index": "products",
  "_id": "100",
  "result": "updated"
}
```
👆 **Result `"updated"` batata hai ki document successfully modify ho gaya.**  

### ❗ **Note:**  
Agar hum wahi value dubara assign karein jo pehle se hai, to `"result": "noop"` return hoga.  
Matlab **document me koi change nahi hua**.  

#### **Example: No Change (Same Value Assigned)**
```json
POST /products/_update/100
{
  "doc": {
    "in_stock": 3
  }
}
```
#### **Response:**
```json
{
  "_index": "products",
  "_id": "100",
  "result": "noop"
}
```

---

## ➕ **Adding a New Field**  

Elasticsearch me **update ke time new fields bhi add** kar sakte hain.  

🔹 **Syntax:**  
```json
POST /index_name/_update/document_id
{
  "doc": {
    "new_field": "value"
  }
}
```

### ✅ **Example: Add `tags` Field**
Hum `tags` naam ka ek **new field** add karenge jo ek **array of strings** hoga.  

#### **Request:**
```json
POST /products/_update/100
{
  "doc": {
    "tags": ["electronics", "smartphone"]
  }
}
```
  
#### **Response:**
```json
{
  "_index": "products",
  "_id": "100",
  "result": "updated"
}
```

👆 **Ab document me ek new field `"tags"` bhi aa gaya.**  
Agar hum `GET /products/_doc/100` karenge, to **updated document** milega.  

#### **Expected Document After Update:**
```json
{
  "name": "Smartphone",
  "price": 25000,
  "in_stock": 3,
  "tags": ["electronics", "smartphone"]
}
```

---

## 🛠️ **How Updates Work Internally**  

🔹 Elasticsearch ke documents **immutable** hote hain, iska matlab **direct update possible nahi hota**.  
🔹 Jab hum document **update** karte hain, Elasticsearch actually:  
  1. **Pura document retrieve karta hai**  
  2. **Uske fields ko update karta hai**  
  3. **Ek new document create karke old document ko replace kar deta hai**  

Yahi reason hai ki Elasticsearch me **frequent updates avoid karne chahiye**, kyunki **har update me reindexing hoti hai**, jo performance par impact dal sakti hai.  

### ⚡ **Optimized Update Approach:**  
Agar **multiple fields update karni ho**, to **ek single update request me** sab update karo, **baar-baar update avoid karo**.  

---

## 📝 **Examples with Expected Output**  

### ✅ **1. Update an Existing Field (`in_stock`)**
#### **Request:**
```json
POST /products/_update/100
{
  "doc": {
    "in_stock": 3
  }
}
```
#### **Response:**
```json
{
  "_index": "products",
  "_id": "100",
  "result": "updated"
}
```

---

### ✅ **2. Add a New Field (`tags`)**
#### **Request:**
```json
POST /products/_update/100
{
  "doc": {
    "tags": ["electronics", "smartphone"]
  }
}
```
#### **Response:**
```json
{
  "_index": "products",
  "_id": "100",
  "result": "updated"
}
```

---

### ✅ **3. No Update (Same Value Assigned)**
#### **Request:**
```json
POST /products/_update/100
{
  "doc": {
    "in_stock": 3
  }
}
```
#### **Response:**
```json
{
  "_index": "products",
  "_id": "100",
  "result": "noop"
}
```

---

## 🎯 **Best Practices**  

✔️ **Frequent updates avoid karo** kyunki har baar document reindex hota hai.  
✔️ **Agar ek se zyada fields update karni ho, to ek hi request me sab update karo**.  
✔️ **Document structure ko pehle se plan karo** taaki frequent updates na karni padein.  
✔️ **Update API ka use karo, instead of retrieving & reindexing manually**, taaki network latency kam ho.  

---

