# 📌 **Table of Contents**  

1️⃣ [🔍 Searching Approaches in Elasticsearch](#1)  
2️⃣ [🌐 URI Search vs Query DSL](#2)  
3️⃣ [📝 Writing Your First Query DSL Search](#3)  
4️⃣ [📊 Understanding the Search API Response](#4)  
5️⃣ [🔢 Breakdown of the Response Keys](#5)  
6️⃣ [✅ Example: Running a Match All Query](#6)  
7️⃣ [📌 Key Takeaways and What's Next](#7)  

---

## 1️⃣ 🔍 **Searching Approaches in Elasticsearch**  <a id="1"></a>

Elasticsearch me data ko search karne ke **do tareeke** hote hain:  

1. **URI Search** – Query ko URL parameters me pass karna  
2. **Query DSL** – Query ko **JSON request body** me likhna  

**Query DSL** ko prefer kiya jata hai kyunki:  
✅ Ye **structured** hoti hai aur zyada powerful hoti hai  
✅ Saare **Elasticsearch ke search features** ko support karti hai  
✅ JSON format me likhne ki wajah se **readable aur maintainable** hoti hai  

---

## 2️⃣ 🌐 **URI Search vs Query DSL**  <a id="2"></a>

| Feature        | URI Search 🌍 | Query DSL 📝 |
|---------------|-------------|-------------|
| Query Format  | **Query String (Lucene Syntax)** | **JSON Request Body** |
| Power         | Limited features ⚠️ | Full search capabilities 🚀 |
| Readability   | Less readable ❌ | More structured & readable ✅ |
| Usage        | Rarely used 📉 | Preferred approach 📈 |

🔴 **Why is URI search not covered?**  
- URI search **Lucene query string syntax** use karta hai, jo **complex aur limited** hai.  
- Query DSL **feature-rich hai** aur **real-world applications me use hota hai**.  

---

## 3️⃣ 📝 **Writing Your First Query DSL Search**  <a id="3"></a>

Chalo ek **basic search query** likhte hain jo **saare documents** ko return kare! 🛠️  

### 🔹 **Request** (Match All Query)  
```json
GET my_index/_search
{
  "query": {
    "match_all": {}
  }
}
```

### 🔹 **Explanation**  
- `"query"` → Isme **search condition** define hoti hai  
- `"match_all"` → Ye **sabhi documents ko match** karega  

---

## 4️⃣ 📊 **Understanding the Search API Response**  <a id="4"></a>

Jab hum query run karenge, to Elasticsearch **JSON format me response** dega:  

```json
{
  "took": 5,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 100,
      "relation": "eq"
    },
    "max_score": 1.0,
    "hits": [
      {
        "_index": "my_index",
        "_id": "1",
        "_score": 1.0,
        "_source": {
          "name": "Elasticsearch Basics",
          "category": "Search Engine"
        }
      }
    ]
  }
}
```

---

## 5️⃣ 🔢 **Breakdown of the Response Keys**  <a id="5"></a>

| **Key**        | **Explanation** |
|---------------|---------------|
| **`took`**     | Request ko execute karne ka **time (milliseconds me)** |
| **`timed_out`** | Request timeout hua ya nahi (**true/false**) |
| **`_shards`**  | Query me involve **shards ka breakdown** |
| **`hits.total`** | **Total matching documents** |
| **`max_score`** | Sabse **high relevance score** wala document |
| **`hits`** | **Matched documents** ki list |

📌 **Important Note**  
- Agar `"timed_out": true` ho to iska matlab **query slow chal rahi hai**.  
- `"hits.total.value"` bata raha hai ki **kitne documents match hue**.  
- `"max_score"` relevance scoring batata hai, jo **ranking ke liye important hai**.  

---

## 6️⃣ ✅ **Example: Running a Match All Query**  <a id="6"></a>

Chalo ek example dekhte hain jisme **humara index `products` hai**:  

### 🔹 **Request**  
```json
GET products/_search
{
  "query": {
    "match_all": {}
  }
}
```

### 🔹 **Expected Output**  
```json
{
  "took": 10,
  "timed_out": false,
  "hits": {
    "total": {
      "value": 5,
      "relation": "eq"
    },
    "hits": [
      {
        "_index": "products",
        "_id": "1",
        "_score": 1.0,
        "_source": {
          "name": "iPhone 14",
          "category": "Mobile",
          "price": 799
        }
      },
      {
        "_index": "products",
        "_id": "2",
        "_score": 1.0,
        "_source": {
          "name": "MacBook Pro",
          "category": "Laptop",
          "price": 1299
        }
      }
    ]
  }
}
```

📌 **Analysis of Response:**  
✅ **5 documents matched**  
✅ `"hits"` me **sabhi matched documents** aaye  
✅ **_score** 1.0 hai kyunki **sabhi documents match ho rahe hain**  

---

## 7️⃣ 📌 **Key Takeaways and What's Next**  <a id="7"></a>

🔹 **Searching ke do tareeke hote hain**: URI search & Query DSL  
🔹 **Query DSL zyada powerful aur preferred approach hai**  
🔹 **Match All Query se sabhi documents return hote hain**  
🔹 **Response me relevance scoring, shard details aur matched documents aate hain**  

---

