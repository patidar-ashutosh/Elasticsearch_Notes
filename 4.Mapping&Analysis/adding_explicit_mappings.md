### 📌 **Adding Explicit Mappings in Elasticsearch**  

#### 📖 **Table of Contents**  
1️⃣ [Introduction](#1)  
2️⃣ [Explicit Mapping vs Dynamic Mapping](#2)  
3️⃣ [Index Banate Time Mapping Kaise Add Karein?](#3)  
4️⃣ [Fields Ko Kaise Define Karein?](#4)  
5️⃣ [Nested Objects Ka Handling](#5)  
6️⃣ [Text vs Keyword Data Type](#6)  
7️⃣ [Coercion Ka Impact](#7)  
8️⃣ [Best Practices for Field Naming](#8)  
9️⃣ [Example Query & Expected Output](#9)  
🔟 [Conclusion](#10)  

---  

## 🏗️ **1. Introduction**  <a id="1"></a>
Elasticsearch me mapping ka kaam hota hai data ke structure ko define karna. Jab hum data index karte hain, to Elasticsearch automatically mapping assign kar sakta hai (Dynamic Mapping), ya phir hum khud se explicitly mapping define kar sakte hain.  

**Explicit Mapping** ka fayda yeh hota hai ki hum data types aur field properties ko control kar sakte hain, jo ki aggregation, searching aur indexing performance improve karta hai.  

---

## ⚖️ **2. Explicit Mapping vs Dynamic Mapping**  <a id="2"></a>
- **Dynamic Mapping** – Agar hum koi field explicitly define nahi karte to Elasticsearch khud hi uska data type detect kar leta hai.  
- **Explicit Mapping** – Hum khud define karte hain ki kaunsa field kaunsa type ka hoga. Yeh best practice hai agar hume data consistency aur performance control karni ho.  

🔹 **Example:**  
```json
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "age": { "type": "integer" }
    }
  }
}
```

---

## 🏗️ **3. Index Banate Time Mapping Kaise Add Karein?**  <a id="3"></a>
Jab hum ek **naya index** create karte hain, tab hum mapping ko explicitly define kar sakte hain using `"mappings"` key.

**Example:**  
```json
PUT reviews
{
  "mappings": {
    "properties": {
      "rating": { "type": "float" },
      "content": { "type": "text" },
      "product_id": { "type": "integer" },
      "author": {
        "type": "object",
        "properties": {
          "first_name": { "type": "text" },
          "last_name": { "type": "text" },
          "email": { "type": "keyword" }
        }
      }
    }
  }
}
```

---

## 🏷️ **4. Fields Ko Kaise Define Karein?**  <a id="4"></a>
Har field ko **"properties"** key ke andar define karna zaroori hai. Har field ka **data type** specify karna chahiye.  

🔹 **Common Data Types**  
| Data Type | Description |
|-----------|------------|
| `text` | Full-text search ke liye |
| `keyword` | Exact match & aggregations ke liye |
| `integer` | Numbers ke liye |
| `float` | Decimal values ke liye |
| `boolean` | True/False values ke liye |
| `date` | Date values ke liye |
| `object` | Nested data ke liye |

---

## 🏛️ **5. Nested Objects Ka Handling**  <a id="5"></a>
Agar kisi field ke andar aur bhi fields hain, to hume `"properties"` key use karni padegi.  

🔹 **Example of Nested Object:**  
```json
"author": {
  "type": "object",
  "properties": {
    "first_name": { "type": "text" },
    "last_name": { "type": "text" },
    "email": { "type": "keyword" }
  }
}
```
💡 **Tip:** Elasticsearch me hum deeply nested objects bhi bana sakte hain. Lekin bohot zyada nesting performance slow kar sakti hai.

---

## 🔍 **6. Text vs Keyword Data Type**  <a id="6"></a>
Elasticsearch me **"text"** aur **"keyword"** kaafi alag alag kaam ke liye use hote hain.  

| Feature | `text` | `keyword` |
|---------|--------|----------|
| Full-Text Search | ✅ | ❌ |
| Exact Match | ❌ | ✅ |
| Aggregations | ❌ | ✅ |
| Sorting | ❌ | ✅ |

🔹 **Example:**  
```json
"email": { "type": "keyword" }
```
📌 **Explanation:** Email field ko `"keyword"` type diya kyunki hum ispe full-text search nahi karenge, balki exact match ya aggregation karna hoga.

---

## ⚠️ **7. Coercion Ka Impact**  <a id="7"></a>
Elasticsearch automatically kuch data types ko convert kar leta hai agar coercion enabled ho.  

🔹 **Example:**  
Agar hum `price` ko integer define karte hain, lekin document me `"19.99"` string bhejte hain, to Elasticsearch usko convert kar dega.  

```json
PUT products
{
  "mappings": {
    "properties": {
      "price": { "type": "integer" }
    }
  }
}
```
```json
POST products/_doc/1
{
  "price": "100"
}
```
✅ Elasticsearch isko accept karega aur `"100"` ko integer me convert kar dega.

⚠️ **Lekin agar hum coercion disable kar dein, to yeh document reject ho jayega.**  

```json
"price": { "type": "integer", "coerce": false }
```

---

## 🔠 **8. Best Practices for Field Naming**  <a id="8"></a>
- **Consistency Rakho:** Sab fields ek jaisa format follow karein.  
- **camelCase ya snake_case Use Karo:**  
  - `firstName` (camelCase)  
  - `first_name` (snake_case)  
- **Avoid Spaces & Special Characters:**  
  - ❌ `"First Name"` – Yeh avoid karo  
  - ✅ `"first_name"` – Yeh best practice hai  

---

## 📝 **9. Example Query & Expected Output**  <a id="9"></a>

### 📌 **Step 1: Create Index with Mapping**  
```json
PUT reviews
{
  "mappings": {
    "properties": {
      "rating": { "type": "float" },
      "content": { "type": "text" },
      "product_id": { "type": "integer" },
      "author": {
        "type": "object",
        "properties": {
          "first_name": { "type": "text" },
          "last_name": { "type": "text" },
          "email": { "type": "keyword" }
        }
      }
    }
  }
}
```

### 📌 **Step 2: Insert Document**  
```json
POST reviews/_doc/1
{
  "rating": 4.5,
  "content": "Awesome product!",
  "product_id": 12345,
  "author": {
    "first_name": "John",
    "last_name": "Doe",
    "email": "john.doe@example.com"
  }
}
```
✅ **Expected Output:**
```json
{
  "_index": "reviews",
  "_id": "1",
  "_version": 1,
  "result": "created"
}
```

### 📌 **Step 3: Invalid Data Handling**  
Agar hum email ko object me convert kar dein:  
```json
POST reviews/_doc/2
{
  "rating": 4.5,
  "content": "Awesome product!",
  "product_id": 12345,
  "author": {
    "first_name": "John",
    "last_name": "Doe",
    "email": { "value": "john.doe@example.com" }
  }
}
```
⚠️ **Error:**  
```json
{
  "error": {
    "reason": "email field must be of type keyword"
  }
}
```
✅ Elasticsearch isko reject kar dega kyunki `"email"` field ka type keyword hai, aur humne usme object bheja.

---

## 🎯 **10. Conclusion**  <a id="10"></a>
- **Explicit Mapping** ka use karna best practice hai.  
- **Fields ka type carefully select karo** based on searching & aggregations.  
- **Coercion samajhna zaroori hai** taaki unexpected errors avoid ho sakein.  
- **Nested objects ka dhyan rakho**, unnecessary nesting avoid karo.  
- **Field naming conventions follow karo** taaki consistency bani rahe.  

