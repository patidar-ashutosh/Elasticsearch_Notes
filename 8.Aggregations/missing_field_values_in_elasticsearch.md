## ❌ **Missing Field Values in Elasticsearch**  

**Missing Aggregation** Elasticsearch me ek bucket aggregation hai jo **un documents ko count karti hai jisme kisi specific field ki value nahi hai ya null hai**.  

🚀 **Key Idea:**  
Agar tum ek aggregation kar rahe ho kisi field pe (jaise `status`), lekin kuch documents me wo field **missing hai ya null hai**, to normal aggregation unhe ignore karegi.  
**Missing Aggregation** ka use karke tum **aise documents ko ek alag bucket me count kar sakte ho.**  

---

## 📌 **Table of Contents**  

1️⃣ [🔍 Missing Aggregation Kya Hai?](#1-missing-aggregation-kya-hai)  
2️⃣ [⚙️ Missing Aggregation Kaise Kaam Karti Hai?](#2-missing-aggregation-kaise-kaam-karti-hai)  
3️⃣ [📝 Example: Missing Aggregation](#3-example-missing-aggregation)  
4️⃣ [📊 Combining Missing Aggregation with Other Aggregations](#4-combining-missing-aggregation-with-other-aggregations)  
5️⃣ [⚠️ Important Notes](#5-important-notes)  

---

## 1️⃣ 🔍 **Missing Aggregation Kya Hai?**  

Jab hum kisi specific field par aggregation apply karte hain, to agar kisi document me wo field **nahi hai** ya **null hai**, to normal aggregation usse consider nahi karegi.  

✅ **Missing Aggregation** ka use karke tum **un sabhi documents ko ek bucket me rakh sakte ho** jisme wo field missing hai ya null hai.  
✅ Ye aggregation **data validation aur missing data analysis** ke liye kaafi useful hoti hai.  

---

## 2️⃣ ⚙️ **Missing Aggregation Kaise Kaam Karti Hai?**  

🔹 **Yeh ek bucket aggregation hai**, jo **missing ya null values wale documents ka count** karti hai.  
🔹 **Sirf un documents ko consider karti hai jisme specified field missing hai ya null hai.**  
🔹 **Sub-aggregations** ke saath use kiya ja sakta hai (e.g., total amount ka sum).  

---

## 3️⃣ 📝 **Example: Missing Aggregation**  

### **Step 1: Test Data Insert Karna**  
Pehle hum **kuch aise documents insert karenge jisme `status` field missing hai ya null hai**.  

```json
POST /orders/_doc/1001
{
  "total_amount": 150
}

POST /orders/_doc/1002
{
  "total_amount": 150,
  "status": null
}
```

---

### **Step 2: Missing Aggregation Query**  

Ab hum **missing aggregation** run karenge jo **un documents ko count karegi jisme `status` field missing hai ya null hai**.  

```json
{
  "aggs": {
    "orders_without_status": {
      "missing": {
        "field": "status"
      }
    }
  }
}
```

✅ **Yeh aggregation ek bucket banayegi jo sirf un documents ko count karegi jisme `status` field missing hai ya null hai.**  

---

### **Step 3: Expected Output**  

```json
{
  "aggregations": {
    "orders_without_status": {
      "doc_count": 2
    }
  }
}
```

✅ **`doc_count: 2` ka matlab hai ki do documents aise hain jisme `status` field missing hai ya null hai.**  

---

## 4️⃣ 📊 **Combining Missing Aggregation with Other Aggregations**  

Ab hum **missing aggregation ko sub-aggregation ke saath use karenge** taaki **un documents ka total amount sum** kar sakein jisme `status` field missing hai.  

```json
{
  "aggs": {
    "orders_without_status": {
      "missing": {
        "field": "status"
      },
      "aggs": {
        "missing_sum": {
          "sum": {
            "field": "total_amount"
          }
        }
      }
    }
  }
}
```

---

### **Expected Output**  

```json
{
  "aggregations": {
    "orders_without_status": {
      "doc_count": 2,
      "missing_sum": {
        "value": 300
      }
    }
  }
}
```

✅ **Yeh aggregation hume batayegi ki total amount un orders ka jo `status` field miss kar rahe hain.**  
✅ **Yaha total sum `300` hai kyunki dono documents ka `total_amount` 150-150 tha.**  

---

## 5️⃣ ⚠️ **Important Notes**  

🔹 **Missing Aggregation sirf un documents ko count karti hai jisme field missing ho ya null ho.**  
🔹 **Agar field ka default mapping me `null_value` set hai, to wo consider kiya jayega.**  
🔹 **Data validation ke liye useful hai, taaki invalid ya incomplete records identify kiye ja sakein.**  

---

## 🎯 **Conclusion**  

✅ **Missing Aggregation** ka use **un documents ko find karne ke liye hota hai jisme koi field missing ya null ho.**  
✅ **Isse hum easily missing values ka analysis kar sakte hain aur data completeness check kar sakte hain.**  
✅ **Hum isse sub-aggregations ke saath combine karke additional insights le sakte hain.**  

