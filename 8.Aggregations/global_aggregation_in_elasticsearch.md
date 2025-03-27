## 🌍 **Global Aggregation in Elasticsearch**  

**Global aggregation** Elasticsearch me ek special type ki aggregation hai jo **query ke scope se bahar nikal kar** index ke **sabhi documents** ko consider karti hai.  

🚀 **Key Idea:**  
Agar tum ek **query apply karte ho** jo documents ko filter karti hai, to normal aggregations **sirf filtered documents par apply hoti hain**.  
Lekin **Global Aggregation** ka use karne se **poore index ke documents** aggregate ho jate hain, chahe query kisi specific set ko filter kar rahi ho.  

---

## 📌 **Table of Contents**  

1️⃣ [🔍 Global Aggregation Kya Hai?](#1)  
2️⃣ [⚙️ Global Aggregation Kaise Kaam Karta Hai?](#2)  
3️⃣ [📝 Example: Global Aggregation](#3)  
4️⃣ [📊 Comparing Global & Normal Aggregation](#4)  
5️⃣ [⚠️ Important Notes](#5)  
6️⃣ [🎯 Conclusion](#6)

---

## 1️⃣ 🔍 **Global Aggregation Kya Hai?**  <a id="1"></a>

Jab tum ek query Elasticsearch me run karte ho, to aggregation **sirf wahi documents consider karti hai** jo query ke filter ke andar aate hain.  

Lekin **Global Aggregation** is restriction ko tod kar **poore index ke documents** par apply hoti hai.  

👀 **Example:**  
Maan lo ki humare paas ek index hai jo **orders** store karta hai, aur hum sirf **total_amount >= 100** wale documents ko aggregate karna chahte hain.  

🛑 **Normal Aggregation** → Sirf filtered documents pe kaam karegi.  
✅ **Global Aggregation** → Poore index pe kaam karegi, bina kisi filter ke.  

---

## 2️⃣ ⚙️ **Global Aggregation Kaise Kaam Karta Hai?**  <a id="2"></a>

🔹 **Normal aggregations** sirf filtered documents pe kaam karti hain.  
🔹 **Global aggregation** index ke **sabhi documents** ko consider karti hai, chahe koi filter laga ho ya na ho.  
🔹 Global aggregation ko **top-level** pe define karna zaroori hota hai.  
🔹 Is aggregation ke andar **sub-aggregations** define ki ja sakti hain.  

---

## 3️⃣ 📝 **Example: Global Aggregation**  <a id="3"></a>

👀 **Query Without Global Aggregation**  

```json
{
  "query": {
    "range": {
      "total_amount": { "gte": 100 }
    }
  },
  "aggs": {
    "stats_expensive": {
      "stats": {
        "field": "total_amount"
      }
    }
  }
}
```

✅ Yeh aggregation sirf **total_amount >= 100** wale **filtered documents** par kaam karegi.  
✅ **Agar total 1000 documents hain aur 489 is filter me aate hain, to aggregation sirf 489 documents pe kaam karegi.**  

---

👀 **Query With Global Aggregation**  

```json
{
  "query": {
    "range": {
      "total_amount": { "gte": 100 }
    }
  },
  "aggs": {
    "all_orders": {
      "global": {}, 
      "aggs": {
        "stats_amounts": {
          "stats": {
            "field": "total_amount"
          }
        }
      }
    }
  }
}
```

✅ **Yeh aggregation filter se bahar nikal kar poore 1000 documents pe apply hogi.**  
✅ `global` aggregation **query ke influence me nahi aati**, aur **poore index** ke documents par apply hoti hai.  

🔹 **Expected Output:**  

```json
{
  "aggregations": {
    "all_orders": {
      "doc_count": 1000,
      "stats_amounts": {
        "count": 1000,
        "min": 10,
        "max": 5000,
        "avg": 1200,
        "sum": 1200000
      }
    },
    "stats_expensive": {
      "count": 489,
      "min": 100,
      "max": 5000,
      "avg": 2000,
      "sum": 978000
    }
  }
}
```

🔹 `all_orders.doc_count = 1000` → **Poore index ke documents count ho rahe hain.**  
🔹 `stats_expensive.doc_count = 489` → **Query ke filtered documents ka count.**  

---

## 4️⃣ 📊 **Comparing Global & Normal Aggregation**  <a id="4"></a>

| Feature               | Normal Aggregation            | Global Aggregation              |  
|-----------------------|-----------------------------|---------------------------------|  
| **Scope**            | Filtered documents pe apply hoti hai | Poore index ke documents pe apply hoti hai |  
| **Query Dependence** | Query ke filter me hi kaam karti hai | Query ke filter ko ignore karti hai |  
| **Use Case**        | Sirf ek subset analyze karna ho | Poore index ka overview chahiye ho |  
| **Example**         | `stats_expensive` aggregation | `all_orders` aggregation |  

✅ **Global Aggregation kab use karein?**  
- Jab **query ke scope se bahar nikal kar** aggregation karni ho.  
- Jab **poore index ka statistical overview** chahiye ho.  
- Jab tum **comparison karna chahte ho** ki **filtered documents vs all documents** ka kya difference hai.  

---

## 5️⃣ ⚠️ **Important Notes**  <a id="5"></a>

🔹 **Global Aggregation hamesha top-level pe hoti hai.**  
🔹 **Sub-aggregations** ko allow karti hai, lekin khud kisi aur aggregation ka part nahi ban sakti.  
🔹 **Query se isolated hoti hai**, isliye normal search filters ispe apply nahi hote.  

---

## 6️⃣ 🎯 **Conclusion**  <a id="6"></a>

✅ **Global Aggregation** query ke scope se bahar nikal kar **poore index ke documents pe kaam karti hai.**  
✅ **Iska use tab hota hai jab filtered documents aur full dataset ka comparison karna ho.**  
✅ **Query ke influence se free hoti hai**, aur **top-level aggregation** ke roop me kaam karti hai.  

