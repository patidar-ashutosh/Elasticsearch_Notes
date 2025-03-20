## 🏺 **Nested Aggregations in Elasticsearch**  

### 🔎 **Introduction**  
Elasticsearch me **aggregations** ek powerful feature hai jo structured data ko analyze karne me madad karti hai.  

hum **nested aggregations** ke concept ko explore karenge, jo aggregations ke andar aggregations create karne ka tareeka hai.  

---

## 📜 **Table of Contents**  
1️⃣ [Bucket vs Metric Aggregations 🪣📊](#bucket-vs-metric-aggregations)  
2️⃣ [Nested Aggregations kya hai? 🔄](#nested-aggregations-kya-hai)  
3️⃣ [Example: Terms + Stats Aggregation 📝](#example-terms--stats-aggregation)  
4️⃣ [Aggregation Context & Query Filtering 🔍](#aggregation-context--query-filtering)  
5️⃣ [Recap: Key Takeaways 🔄](#recap-key-takeaways)  

---

## 🪣📊 **1. Bucket vs Metric Aggregations**  
Elasticsearch me aggregations do types ki hoti hain:  
| Aggregation Type | Description | Example |
|------------------|------------|---------|
| **Bucket Aggregation** 🪣 | Documents ko **buckets** me group karta hai | Terms Aggregation |
| **Metric Aggregation** 📊 | Numerical values ka **statistical calculation** karta hai | Sum, Avg, Stats Aggregation |

➡ **Bucket aggregations** ke andar **metric aggregations** nest ho sakti hain, jo hume **hierarchical analysis** karne ka moka deti hai.  

---

## 🔄 **2. Nested Aggregations kya hai?**  
Nested aggregations ka concept **recursion** jaisa hota hai.  
Matlab ek **bucket aggregation** ke andar ek **metric aggregation** ya aur ek **bucket aggregation** ho sakti hai.  

🎯 **Use Case:**  
Agar tumhe kisi **order dataset** me different **statuses (processed, pending, cancelled)** ke **total amount** ka sum nikalna ho, to tum **terms aggregation + stats aggregation** use kar sakte ho.  

---

## 📝 **3. Example: Terms + Stats Aggregation**  
💡 **Scenario:** Hume orders ke **status** ke basis pe unka **total_amount** ka stats nikalna hai.  

### ✅ **Query:**
```json
{
  "size": 0,
  "aggs": {
    "status_buckets": {
      "terms": { "field": "status.keyword" },
      "aggs": {
        "status_stats": {
          "stats": { "field": "total_amount" }
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
    "status_buckets": {
      "buckets": [
        {
          "key": "processed",
          "doc_count": 209,
          "status_stats": {
            "count": 209,
            "min": 10.27,
            "max": 281.77,
            "avg": 145.32,
            "sum": 30372.88
          }
        },
        {
          "key": "pending",
          "doc_count": 120,
          "status_stats": {
            "count": 120,
            "min": 5.12,
            "max": 250.99,
            "avg": 130.56,
            "sum": 15667.20
          }
        }
      ]
    }
  }
}
```
🧐 **Explanation:**  
- **status_buckets** aggregation status ke basis pe documents ko **buckets** me daal raha hai.  
- **status_stats** aggregation uss bucket ke andar **stats** nikal raha hai (min, max, avg, sum).  
- **Har bucket ka result usi status ke liye specific hoga.**  

---

## 🔍 **4. Aggregation Context & Query Filtering**  
Aggregations **context** ke basis pe run hoti hain.  

### 🎯 **Example: Sirf ₹100 se upar wale orders ke liye aggregation run karna**  
```json
{
  "size": 0,
  "query": {
    "range": {
      "total_amount": { "gte": 100 }
    }
  },
  "aggs": {
    "status_buckets": {
      "terms": { "field": "status.keyword" },
      "aggs": {
        "status_stats": {
          "stats": { "field": "total_amount" }
        }
      }
    }
  }
}
```
➡ **Ab aggregation sirf un documents par chalegi jisme `total_amount >= 100` hoga.**  
➡ **Bucket counts change ho jayenge kyunki ab kam documents match honge.**  

---

## 🔄 **5. Recap: Key Takeaways**  
✔ **Metric aggregations (Stats, Sum, Avg)** simple numerical results deti hain.  
✔ **Bucket aggregations (Terms, Filters)** documents ko groups me divide karti hain.  
✔ **Bucket aggregations ke andar aur aggregations nest ho sakti hain.**  
✔ **Aggregations apne context ke basis pe run hoti hain (query level ya bucket level).**  
✔ **Nested aggregations hierarchical analysis ko easy banati hain.**  

---

## 🎯 **Conclusion**  
-   Nested aggregations ka use **complex analysis** ke liye kiya jata hai.  
-   Ab tum multiple aggregations ko combine karke **deep insights** nikal sakte ho.  
