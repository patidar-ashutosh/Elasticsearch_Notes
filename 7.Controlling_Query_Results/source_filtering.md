# 📌 Source Filtering

## 📖 Table of Contents  

1️⃣ **[🔍 Introduction: Why Source Filtering?](#1)**  
2️⃣ **[❌ Disabling `_source` Field Completely](#2)**  
3️⃣ **[🎯 Selecting Specific Fields to Return](#3)**  
4️⃣ **[📌 Filtering Nested Objects & Wildcards](#4)**  
5️⃣ **[⚙️ Advanced Source Filtering (Includes & Excludes)](#5)**  
6️⃣ **[🛠️ Comparison with SQL (`SELECT` Statement)](#6)**  
7️⃣ **[🔚 Summary & Key Takeaways](#7)**  

---

## 1️⃣ 🔍 Introduction: Why Source Filtering?  <a id="1"></a>

🔹 Elasticsearch me **_source field by default pura document return karta hai**, lekin agar tumhe sirf kuch specific fields chahiye, to **source filtering ka use karke response size optimize kar sakte ho.**  

### **⚡ Source Filtering ke Benefits:**  
✔️ **Response Size Reduce Hota Hai** – Kam data transfer hone se network load kam hota hai.  
✔️ **Query Performance Improve Hoti Hai** – Unnecessary fields ko exclude karne se query fast execute hoti hai.  
✔️ **Security aur Privacy** – Sensitive data ko filter kar sakte ho.  

---

## 2️⃣ ❌ Disabling `_source` Field Completely  <a id="2"></a>

Agar tum **sirf document ID retrieve karna chahte ho** aur pura `_source` data nahi chahiye, to `_source` ko **false** set kar sakte ho.  

### **🚀 Example (Disable `_source`)**
```json
GET my_index/_search
{
  "_source": false,
  "query": {
    "match_all": {}
  }
}
```
🛠️ **Response (Without `_source` Field):**  
```json
{
  "hits": {
    "hits": [
      { "_id": "1" },
      { "_id": "2" }
    ]
  }
}
```
✔️ **Useful for searching document IDs without extra data.**  

---

## 3️⃣ 🎯 Selecting Specific Fields to Return  <a id="3"></a>

Agar tum sirf **ek ya kuch specific fields chahte ho**, to `_source` me unka naam specify kar sakte ho.  

### **🚀 Example (Single Field)**  
```json
GET my_index/_search
{
  "_source": "created_at",
  "query": {
    "match_all": {}
  }
}
```
🛠️ **Response:**  
```json
{
  "hits": {
    "hits": [
      { "_id": "1", "_source": { "created_at": "2024-03-07" } },
      { "_id": "2", "_source": { "created_at": "2024-03-06" } }
    ]
  }
}
```
✔️ **Sirf `created_at` field return ho raha hai!**  

### **🚀 Example (Multiple Fields)**  
```json
GET my_index/_search
{
  "_source": ["name", "email"],
  "query": {
    "match_all": {}
  }
}
```
✔️ **`name` aur `email` fields return honge, baki sab exclude hoga.**  

---

## 4️⃣ 📌 Filtering Nested Objects & Wildcards  <a id="4"></a>

Agar tum nested objects me **kisi specific field** ko access karna chahte ho, to **dot notation** use kar sakte ho.  

### **🚀 Example (Specific Nested Field)**  
```json
GET my_index/_search
{
  "_source": "ingredients.name",
  "query": {
    "match_all": {}
  }
}
```
🛠️ **Response:**  
```json
{
  "hits": {
    "hits": [
      { "_source": { "ingredients": { "name": "Sugar" } } },
      { "_source": { "ingredients": { "name": "Salt" } } }
    ]
  }
}
```
✔️ **Sirf `ingredients.name` field return ho raha hai!**  

### **🚀 Example (All Fields in Nested Object - Using `*`)**  
```json
GET my_index/_search
{
  "_source": "ingredients.*",
  "query": {
    "match_all": {}
  }
}
```
✔️ **`ingredients` ke andar jitne bhi fields hain, sab return honge.**  

---

## 5️⃣ ⚙️ Advanced Source Filtering (Includes & Excludes)  <a id="5"></a>

Agar tum kuch fields ko **include/exclude** karna chahte ho, to **advanced filtering** ka use kar sakte ho.  

### **🚀 Example (Include All `ingredients` Fields Except `name`)**  
```json
GET my_index/_search
{
  "_source": {
    "includes": ["ingredients.*"],
    "excludes": ["ingredients.name"]
  },
  "query": {
    "match_all": {}
  }
}
```
🛠️ **Response (Excluding `name` field from `ingredients`)**  
```json
{
  "hits": {
    "hits": [
      { "_source": { "ingredients": { "quantity": "2 cups" } } },
      { "_source": { "ingredients": { "quantity": "1 tsp" } } }
    ]
  }
}
```
✔️ **`ingredients.name` exclude ho gaya, baki sab include hai!**  

---

## 6️⃣ 🛠️ Comparison with SQL (`SELECT` Statement)  <a id="6"></a>

Agar tum SQL background se ho, to `_source` filtering **SQL ke `SELECT column_name` jaisa kaam karta hai.**  

| SQL Query  | Elasticsearch Query |
|------------|--------------------|
| `SELECT name, email FROM users;` | `{ "_source": ["name", "email"] }` |
| `SELECT * FROM users;` | `{ "_source": true }` |
| `SELECT * FROM users WHERE age > 30;` | `{ "query": { "range": { "age": { "gt": 30 } } } }` |

✔️ **SQL ke SELECT jaisa hi kaam karta hai Elasticsearch me `_source`.**  

---

## 7️⃣ 🔚 Summary & Key Takeaways  <a id="7"></a>

✅ **Agar `_source` ko `false` set karein, to document ka data return nahi hoga.**  
✅ **Sirf specific fields return karne ke liye `_source`: `"field_name"` ya `_source`: `["field1", "field2"]` use karein.**  
✅ **Nested fields ke liye dot notation (`ingredients.name`) ya wildcard (`ingredients.*`) use kar sakte ho.**  
✅ **Advanced filtering ke liye `includes` aur `excludes` parameters ka use kar sakte ho.**  
✅ **Ye SQL ke `SELECT column_name FROM table_name;` jaisa kaam karta hai.**  

---

