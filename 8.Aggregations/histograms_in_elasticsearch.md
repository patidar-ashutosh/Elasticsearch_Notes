## 📊 Histograms in Elasticsearch  

Histograms Elasticsearch me data ko **dynamic buckets** me divide karne ke liye use hote hain, jo numerical values ka distribution analyze karne me help karte hain. **Range aggregations** ki tarah yaha manually ranges define karne ki zaroorat nahi hoti. Bas ek **fixed interval** specify karna hota hai, aur Elasticsearch automatically buckets bana deta hai.  

---

## 📌 **Table of Contents**  

1️⃣ [📌 Histogram Kya Hota Hai?](#1)  
2️⃣ [⚙️ Histogram Aggregation Kaise Kaam Karta Hai?](#2)  
3️⃣ [📝 Example: Histogram Aggregation](#3)  
4️⃣ [⚙️ Histogram Buckets Customize Kaise Karein?](#4)  
   - [min_doc_count](#4-1)  
   - [extended_bounds](#4-2)  

5️⃣ [📅 Date Histogram Aggregation](#5)  
6️⃣ [🛠️ Offset Parameter Ka Use](#6)  
7️⃣ [🎯 Conclusion](#7)

---

## 1️⃣ 📌 **Histogram Kya Hota Hai?**  <a id="1"></a>

Histogram ek **data visualization technique** hai jo numerical values ko specific **intervals (buckets)** me group karta hai. Iska use tab hota hai jab hume kisi numeric field ka **distribution pattern** samajhna ho.  

👀 **Example:** Agar hume orders ke **total_amount** field ka distribution dekhna hai, to histogram aggregation use karenge jo automatically **25 interval ke gaps** me data ko divide karega.  

💡 **Key Difference from Range Aggregation:**  
- **Range Aggregation** → Pehle se fixed ranges define karni padti hai.  
- **Histogram Aggregation** → Sirf interval specify karna hota hai, aur Elasticsearch khud buckets banata hai.  

---

## 2️⃣ ⚙️ **Histogram Aggregation Kaise Kaam Karta Hai?**  <a id="2"></a>

🔹 Histogram aggregation ek **numeric field** ke values ko specified **intervals** me divide karta hai.  
🔹 Minimum aur maximum values ke beech me **equal-sized buckets** banaye jate hain.  
🔹 **Har document** ko uss bucket me rakha jata hai jisme uska numeric value fit hota hai.  

👀 **Example:**  
Agar humara data 0 se 100 tak ke order amounts ka hai, aur humne **interval = 25** set kiya, to Elasticsearch **automatically** ye buckets banayega:  

| Bucket Key | Interval Range |  
|------------|--------------|  
| 0          | 0 - 24       |  
| 25         | 25 - 49      |  
| 50         | 50 - 74      |  
| 75         | 75 - 99      |  
| 100        | 100 - 124    |  

Agar kisi document ka **total_amount = 60** hai, to ye **50 ke bucket me store hoga**, kyunki values **nearest lower bucket** me round down hoti hain.  

---

## 3️⃣ 📝 **Example: Histogram Aggregation**  <a id="3"></a>

```json
{
  "aggs": {
    "amount_distribution": {
      "histogram": {
        "field": "total_amount",
        "interval": 25
      }
    }
  }
}
```

🔹 Ye aggregation **total_amount field** ko **25 ke interval** me divide karega.  
🔹 Minimum se lekar maximum value tak ke **automatic buckets** generate honge.  

👀 **Expected Output:**  

```json
{
  "aggregations": {
    "amount_distribution": {
      "buckets": [
        { "key": 0, "doc_count": 10 },
        { "key": 25, "doc_count": 15 },
        { "key": 50, "doc_count": 20 },
        { "key": 75, "doc_count": 5 },
        { "key": 100, "doc_count": 2 }
      ]
    }
  }
}
```

✅ Elasticsearch ne automatically **5 buckets** generate ki hain.  
✅ Har bucket me **kitne documents** hain, wo bhi show ho raha hai.  

---

## 4️⃣ ⚙️ **Histogram Buckets Customize Kaise Karein?**  <a id="4"></a>

### 🔹 **1. min_doc_count (Empty Buckets Ko Hatana)**  <a id="4-1"></a>

By default, histogram **har possible bucket** banata hai, even agar usme **koi document na ho**. Agar aap **empty buckets remove** karna chahte ho, to `min_doc_count` set kar sakte ho.  

```json
{
  "aggs": {
    "amount_distribution": {
      "histogram": {
        "field": "total_amount",
        "interval": 25,
        "min_doc_count": 1
      }
    }
  }
}
```

✅ Ye **sirf wahi buckets show karega jisme documents honge**.  
✅ **Empty buckets remove ho jayenge**.  

---

### 🔹 **2. extended_bounds (Specific Range Me Buckets Banana)**  <a id="4-2"></a>

Agar aapko **fixed min aur max range** specify karni ho, to `extended_bounds` use kar sakte ho.  

```json
{
  "aggs": {
    "amount_distribution": {
      "histogram": {
        "field": "total_amount",
        "interval": 25,
        "extended_bounds": { "min": 0, "max": 500 }
      }
    }
  }
}
```

✅ Yeh aggregation **0 se 500 ke beech** me **har possible bucket** banayega, **chahe documents ho ya na ho**.  

---

## 5️⃣ 📅 **Date Histogram Aggregation**  <a id="5"></a>

**Date histogram aggregation** numeric values ki jagah **date fields** ke liye use hota hai. Ye **time-based data analysis** ke liye best hota hai.  

👀 **Example:** Agar hume har month ke orders ka count chahiye, to hum **date_histogram** use karenge.  

```json
{
  "aggs": {
    "orders_over_time": {
      "date_histogram": {
        "field": "purchase_date",
        "calendar_interval": "month"
      }
    }
  }
}
```

✅ **Har month ka orders count** show karega.  
✅ **`calendar_interval`** parameter me hum **month, week, day, hour** specify kar sakte hain.  

---

## 6️⃣ 🛠️ **Offset Parameter Ka Use**  <a id="6"></a>

Kabhi-kabhi hume **histogram buckets ko adjust** karna hota hai, jisme `offset` parameter kaam aata hai.  

👀 **Example:** Agar hum chahte hain ki buckets **1 din aage shift ho**, to:  

```json
{
  "aggs": {
    "orders_over_time": {
      "date_histogram": {
        "field": "purchase_date",
        "calendar_interval": "month",
        "offset": "+1d"
      }
    }
  }
}
```

✅ Isse **har bucket 1 din aage shift** ho jayegi.  
✅ Normal histogram me bhi **offset number specify** karke adjust kar sakte hain.  

---

## 7️⃣ 🎯 **Conclusion**  <a id="7"></a>

✅ **Histogram aggregation** automatically **numeric values ko dynamic buckets** me divide karta hai.  
✅ **min_doc_count** se **empty buckets** remove kiye ja sakte hain.  
✅ **extended_bounds** se **specific range** ki buckets force ki ja sakti hain.  
✅ **Date histogram** time-based analysis ke liye use hota hai.  
✅ **Offset parameter** se buckets ko shift kar sakte hain.  

