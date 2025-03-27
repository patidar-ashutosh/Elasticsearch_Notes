## 🏷️ **Defining Bucket Rules with Filters**  

### 🔎 **Introduction**  
Elasticsearch me **bucket aggregations** kaafi powerful hoti hain, aur **filters aggregation** ek unique tariqa hai **predefined rules ke basis par documents ko buckets me sort karne ka**.  

Hum **filters aggregation** ka use karna sikhenge jisme hum **multiple filter-based buckets** define kar sakte hain aur unpe **sub-aggregations** bhi apply kar sakte hain.  

---

## 📜 **Table of Contents**  
1️⃣ [Filters Aggregation kya hai? 🎯](#1)  
2️⃣ [Example: Recipes ko Buckets me Divide Karna 🍝](#2)  
3️⃣ [Query Execution Flow 🛠️](#3)  
4️⃣ [Sub-Aggregations: Bucket ke Andar Calculation 📊](#4)  
5️⃣ [Recap: Key Takeaways 🔄](#5)  
6️⃣ [Conclusion 🎯](#6)

---

## 🎯 **1. Filters Aggregation kya hai?**  <a id="1"></a>

`filters` aggregation ek **bucket aggregation** hai jo predefined **rules ke basis par documents ko different buckets me place** karti hai.  

🔹 **Filter Aggregation vs Filters Aggregation:**  
| Aggregation Type | Kaam Kya Karti Hai? |  
|-----------------|------------------|  
| `filter` aggregation | Sirf **ek** bucket create karti hai |  
| `filters` aggregation | **Multiple buckets** create karti hai **alag-alag rules** ke basis par |  

🚀 **Use Case:**  
Agar hume **recipe index** me se sirf **pasta aur spaghetti** wale documents alag-alag buckets me store karne hain, to hum **filters aggregation** use karenge.  

---

## 🍝 **2. Example: Recipes ko Buckets me Divide Karna**  <a id="2"></a>

💡 **Scenario:**  
Ek restaurant ke paas **recipe data** hai, aur unhe dekhna hai ki **kitni pasta aur spaghetti recipes** hain aur unki **average rating** kya hai.  

### ✅ **Query:**  
```json
{
  "size": 0,
  "aggs": {
    "recipe_types": {
      "filters": {
        "filters": {
          "pasta": { "match": { "title": "pasta" } },
          "spaghetti": { "match": { "title": "spaghetti" } }
        }
      }
    }
  }
}
```

### 📊 **Expected Output:**
```json
{
  "aggregations": {
    "recipe_types": {
      "buckets": {
        "pasta": { "doc_count": 150 },
        "spaghetti": { "doc_count": 80 }
      }
    }
  }
}
```

🧐 **Explanation:**  
- `"pasta"` bucket me **sirf pasta recipes** aayengi.  
- `"spaghetti"` bucket me **sirf spaghetti recipes** aayengi.  
- `doc_count` batata hai ki **kitne documents** har bucket me aaye hain.  

---

## 🛠️ **3. Query Execution Flow**  <a id="3"></a>

1️⃣ Elasticsearch **filters aggregation** ko process karta hai aur **predefined rules** ke basis par documents ko alag-alag buckets me place karta hai.  
2️⃣ Har bucket ka **document count** return hota hai.  
3️⃣ **Agar sub-aggregations di hain, to unhe bhi process kiya jata hai.**  

---

## 📊 **4. Sub-Aggregations: Bucket ke Andar Calculation**  <a id="4"></a>

Sirf bucket counts lena useful nahi hota, **hume in buckets pe further calculations bhi chahiye**.  
Ab hum **har bucket ke andar average rating nikalenge**.  

### ✅ **Query with Sub-Aggregation:**  
```json
{
  "size": 0,
  "aggs": {
    "recipe_types": {
      "filters": {
        "filters": {
          "pasta": { "match": { "title": "pasta" } },
          "spaghetti": { "match": { "title": "spaghetti" } }
        }
      },
      "aggs": {
        "avg_rating": {
          "avg": { "field": "ratings" }
        }
      }
    }
  }
}
```

### 📊 **Expected Output:**
```json
{
  "aggregations": {
    "recipe_types": {
      "buckets": {
        "pasta": {
          "doc_count": 150,
          "avg_rating": { "value": 4.2 }
        },
        "spaghetti": {
          "doc_count": 80,
          "avg_rating": { "value": 4.5 }
        }
      }
    }
  }
}
```

🧐 **Explanation:**  
- Har bucket ka **document count** ke saath **average rating** bhi aagaya.  
- `"avg_rating"` sub-aggregation sirf **filtered documents** pe apply hui hai.  

---

## 🔄 **5. Recap: Key Takeaways**  <a id="5"></a>

✔ **Filters aggregation predefined rules ke basis par multiple buckets create karti hai.**  
✔ **Har bucket me sirf wahi documents hote hain jo uske rule ko match karte hain.**  
✔ **Sub-aggregations ka use karke hum bucket ke andar further analysis kar sakte hain.**  
✔ **Ye aggregation performance-efficient hoti hai kyunki pehle filtering hoti hai, fir aggregation run hoti hai.**  

---

## 🎯 **6. Conclusion**  <a id="6"></a>

Agar tumhe **data ko custom rules ke basis par divide karna ho**, to `filters` aggregation best choice hai.  
