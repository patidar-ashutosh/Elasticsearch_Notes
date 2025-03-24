## 🔍 Query Execution Contexts in Elasticsearch  

# 📌 **Table of Contents**  
1️⃣ [Introduction](#1)  
2️⃣ [Query Context 🔎](#2)  
3️⃣ [Filter Context 🚦](#3)  
4️⃣ [Performance Optimization 🚀](#4)  
5️⃣ [Real-World Example 🏢](#5)  
6️⃣ [Conclusion 🎯](#6)  

---

## 1. 📌 **Introduction**  <a id="1"></a>

Elasticsearch me **queries do alag-alag contexts** me execute hoti hain:  
1. **Query Context** → **Relevance Score** ka use hota hai.  
2. **Filter Context** → **Binary Matching (Yes/No)** hota hai, aur relevance score calculate nahi hota.  

Samajhne ke liye dono contexts ko alag-alag breakdown karte hain.  

---

## 2. 🔎 **Query Context**  <a id="2"></a>

Ye tab hota hai jab tumhara query **`query` key** ke andar likha hota hai.  

### 🔥 **Key Points:**  
✅ Documents ko relevance score milta hai (**_score** field).  
✅ Documents **descending order** me sort hote hain based on relevance.  
✅ **Text search**, **full-text search**, aur **similarity-based matching** me use hota hai.  

### 📝 **Example:**  
```json
{
  "query": {
    "match": {
      "title": "Elasticsearch tutorial"
    }
  }
}
```
🔹 Is case me, **Elasticsearch** internally **BM25 algorithm** use karta hai, jo **query aur document ka textual similarity** measure karta hai aur uska relevance score assign karta hai.  

---

## 3. 🚦 **Filter Context**  <a id="3"></a>

Ye tab hota hai jab query **binary answer** (Yes/No) return kare bina **relevance score calculate kiye**.  

### ⚡ **Key Points:**  
✅ **Faster execution**, kyunki score calculate nahi hota.  
✅ **Cacheable**, to performance improve hoti hai.  
✅ Structured data (e.g., **dates, numbers, keywords**) ke liye useful hota hai.  
✅ Typically **"filter" key** ke andar likha jata hai.  

### 📝 **Example:**  
```json
{
  "query": {
    "bool": {
      "filter": {
        "term": { "status": "active" }
      }
    }
  }
}
```
🔹 Yahan pe **"status": "active"** match kar raha hai bina score calculate kiye.  

---

## 4. 🚀 **Performance Optimization**  <a id="4"></a>

✔️ **Relevance score ki zarurat nahi?** → **Filter Context** use karo.  
✔️ **Frequent queries ke results cache ho sakte hain?** → **Filter Context** use karo.  
✔️ **Text search aur ranking required hai?** → **Query Context** use karo.  

📌 **Optimization Tip:**  
```json
{
  "query": {
    "bool": {
      "must": { "match": { "title": "Elasticsearch" } }, 
      "filter": { "term": { "status": "published" } }
    }
  }
}
```
🔹 **`must`** me relevance score calculate hoga, **`filter`** me nahi.  
🔹 Ye query optimized hai kyunki **text search aur structured filtering dono efficiently handle ho rahi hain**.  

---

## 5. 🏢 **Real-World Example**  <a id="5"></a>

**E-commerce website** pe tumhe **relevant products** dikhane hain **filters ke sath**.  

```json
{
  "query": {
    "bool": {
      "must": { "match": { "product_name": "laptop" } },
      "filter": [
        { "term": { "brand": "Apple" } },
        { "range": { "price": { "gte": 50000, "lte": 100000 } } }
      ]
    }
  }
}
```
🔹 Yahan pe:  
- **Laptop se related products search ho rahe hain** (**query context**)  
- **Brand aur price range filter ho raha hai** (**filter context**)  

⚡ **Iska benefit?**  
✔️ Faster response time  
✔️ Accurate search results  

## 📊 **Expected Output**  
```json
{
  "hits": {
    "total": 2,
    "hits": [
      {
        "_id": "1",
        "_score": 2.5,
        "_source": {
          "product_name": "MacBook Pro 14-inch",
          "brand": "Apple",
          "price": 95000
        }
      },
      {
        "_id": "2",
        "_score": 1.8,
        "_source": {
          "product_name": "MacBook Air M2",
          "brand": "Apple",
          "price": 85000
        }
      }
    ]
  }
}
```
🔹 **High score wale products top pe aaye**.  
🔹 **Brand aur price range filter hone ke baad sirf 2 results mile**.  

---

## 6. 🎯 **Conclusion**  <a id="6"></a>

✅ **Query Context** → **Relevance scoring hoti hai**, search results ko **ranking** milti hai.  
✅ **Filter Context** → **Binary match hota hai (Yes/No)**, aur **fast execution** hoti hai.  
✅ **Best practice:** **Relevance score ki zarurat ho tabhi Query Context use karo, nahi to Filter Context use karo**.  

