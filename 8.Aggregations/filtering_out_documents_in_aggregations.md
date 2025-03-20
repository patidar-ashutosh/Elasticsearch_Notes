## 🏷️ **Filtering Out Documents in Aggregations**  

### 🔎 **Introduction**  
Elasticsearch aggregations me **filtering** kaafi important hoti hai jab hume sirf kuch specific documents par aggregation run karni ho.  

Hum dekhenge ki **filter aggregations** kaise kaam karti hain aur **aggregation ke context** ko kaise control kiya jata hai.  

---

## 📜 **Table of Contents**  
1️⃣ [Aggregation ka Context kya hota hai? 🌍](#aggregation-ka-context-kya-hota-hai)  
2️⃣ [Filter Aggregation kya hai? 🎯](#filter-aggregation-kya-hai)  
3️⃣ [Example: Low-Value Orders ko Filter Karna 🛒](#example-low-value-orders-ko-filter-karna)  
4️⃣ [Query Execution Flow 🛠️](#query-execution-flow)  
5️⃣ [Recap: Key Takeaways 🔄](#recap-key-takeaways)  

---

## 🌍 **1. Aggregation ka Context kya hota hai?**  
Aggregations **apne context** ke basis pe kaam karti hain.  
- **Top-level aggregation** → Puri dataset pe run hoti hai (e.g., `match_all`).  
- **Nested aggregations** → Apne **parent aggregation ke context** me run hoti hain.  
- **Filter aggregations** → Sirf un documents par run hoti hain jo ek **filter query** ko match karte hain.  

🚀 **Use Case:**  
Agar hume sirf **₹50 se upar wale orders** pe aggregation run karni ho, to **filter aggregation** use karenge.  

---

## 🎯 **2. Filter Aggregation kya hai?**  
Filter aggregation ek **bucket aggregation** hai jo **query filter** apply karke sirf matching documents pe aggregation run karti hai.  
💡 Yeh **search queries** ki tarah hi hoti hai jisme **term, range, match queries** use ki jati hain.  

---

## 🛒 **3. Example: Low-Value Orders ko Filter Karna**  
💡 **Scenario:**  
Hume sirf **₹50 se kam ke orders ko ignore** karna hai aur baaki orders ka **average amount** nikalna hai.  

### ✅ **Query:**
```json
{
  "size": 0,
  "aggs": {
    "high_value_orders": {
      "filter": {
        "range": {
          "total_amount": { "gt": 50 }
        }
      },
      "aggs": {
        "avg_amount": {
          "avg": { "field": "total_amount" }
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
    "high_value_orders": {
      "doc_count": 750,
      "avg_amount": {
        "value": 178.45
      }
    }
  }
}
```

🧐 **Explanation:**  
- **"filter" aggregation** → Sirf ₹50 se upar wale orders ko consider kar rahi hai.  
- **"avg_amount" aggregation** → Sirf **filtered documents** pe average nikal rahi hai.  
- **doc_count: 750** → Matlab 750 orders ₹50 se upar hain.  

---

## 🛠️ **4. Query Execution Flow**  
1️⃣ **Elasticsearch pura dataset fetch karta hai.**  
2️⃣ **Filter aggregation apply hoti hai, jo sirf ₹50 se upar wale orders ko allow karti hai.**  
3️⃣ **Filtered dataset pe avg aggregation apply hoti hai.**  
4️⃣ **Final result return hota hai jo sirf relevant documents ka average dikhata hai.**  

🔍 **Query me mistake hone par kya hoga?**  
Agar tumne galti se `"gte": 50` likh diya `"gt": 50` ki jagah, to **₹50 wale bhi include ho jayenge!**  
Isliye **range queries** likhte waqt dhyan do.  

---

## 🔄 **5. Recap: Key Takeaways**  
✔ **Filter Aggregations sirf matching documents par aggregation run karti hain.**  
✔ **Query filtering aur Aggregation filtering alag cheezein hain.**  
✔ **Filter Aggregations ka use performance improve karta hai kyunki yeh sirf required data par aggregation apply karta hai.**  
✔ **Aggregation ka context important hai: filter ke andar ki aggregation sirf filtered data pe chalegi.**  

---

## 🎯 **Conclusion**  
Filter aggregations kaafi useful hain jab hume **specific subset of documents** par analysis karna ho.  
