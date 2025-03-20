## 📊 **Range Aggregations in Elasticsearch**  

### 🔎 **Introduction**  
Elasticsearch me **documents ko buckets me distribute karne ka ek aur tareeka** hai **range aggregations**. Ye aggregation specific **value ranges ya date ranges** ke basis par documents ko **alagalag buckets me divide** karne ka kaam karti hai.  

### 🔥 **Types of Range Aggregations:**  
1️⃣ **Range Aggregation** - Numeric values ke basis par buckets create karta hai.  
2️⃣ **Date Range Aggregation** - Date values ke basis par buckets create karta hai.  

Hum **dono aggregations ko examples ke sath samjhenge** aur dekhenge **sub-aggregations kaise use kar sakte hain**.  

---

## 📜 **Table of Contents**  
1️⃣ [Range Aggregation kya hai? 🎯](#range-aggregation-kya-hai)  
2️⃣ [Example: Order Amount Distribution 💰](#example-order-amount-distribution)  
3️⃣ [Date Range Aggregation ⏳](#date-range-aggregation)  
4️⃣ [Result Formatting aur Custom Keys 🎨](#result-formatting-aur-custom-keys)  
5️⃣ [Sub-Aggregations: Bucket Statistics 📊](#sub-aggregations-bucket-statistics)  
6️⃣ [Recap: Key Takeaways 🔄](#recap-key-takeaways)  

---

## 🎯 **1. Range Aggregation kya hai?**  
🔹 **Range aggregation** ka use numeric values ko **predefined ranges me split** karne ke liye hota hai.  
🔹 **Date range aggregation** isi concept ko **date fields** pe apply karta hai.  
🔹 **To value exclude hoti hai** (i.e., `to` range me jo value hoti hai, wo bucket me include nahi hoti).  

📌 **Use Case Example:**  
Ek e-commerce store ko dekhna hai ki **kitne orders kis price range me aa rahe hain**.  

---

## 💰 **2. Example: Order Amount Distribution**  
💡 **Scenario:**  
Hume dekhna hai ki **orders ka amount kis range me distribute ho raha hai**:  
- ₹0 - ₹50  
- ₹50 - ₹100  
- ₹100+  

### ✅ **Query:**  
```json
{
  "size": 0,
  "aggs": {
    "amount_distribution": {
      "range": {
        "field": "total_amount",
        "ranges": [
          { "to": 50 },
          { "from": 50, "to": 100 },
          { "from": 100 }
        ]
      }
    }
  }
}
```

### 📊 **Expected Output:**  
```json
{
  "aggregations": {
    "amount_distribution": {
      "buckets": [
        { "key": "*-50.0", "to": 50, "doc_count": 150 },
        { "key": "50.0-100.0", "from": 50, "to": 100, "doc_count": 100 },
        { "key": "100.0-*", "from": 100, "doc_count": 50 }
      ]
    }
  }
}
```

🧐 **Explanation:**  
- Pehli bucket me ₹50 tak ke **150 orders** aaye.  
- Dusri bucket me ₹50 se ₹100 ke **100 orders** aaye.  
- Teesri bucket me ₹100+ ke **50 orders** aaye.  

---

## ⏳ **3. Date Range Aggregation**  
Agar hum **date ke basis pe documents ko distribute** karna chahein, to **date_range aggregation** ka use karenge.  

💡 **Scenario:**  
Hume dekhna hai ki **2016 ke first aur second half me kitne purchases hue**.  

### ✅ **Query:**  
```json
{
  "size": 0,
  "aggs": {
    "purchase_ranges": {
      "date_range": {
        "field": "purchased_at",
        "ranges": [
          { "from": "2016-01-01", "to": "2016-07-01" },
          { "from": "2016-07-01", "to": "2017-01-01" }
        ]
      }
    }
  }
}
```

### 📊 **Expected Output:**  
```json
{
  "aggregations": {
    "purchase_ranges": {
      "buckets": [
        { "key": "2016-01-01 to 2016-07-01", "from": 1451606400000, "to": 1467331200000, "doc_count": 200 },
        { "key": "2016-07-01 to 2017-01-01", "from": 1467331200000, "to": 1483228800000, "doc_count": 180 }
      ]
    }
  }
}
```

🧐 **Explanation:**  
- Pehli bucket me **2016 ke first half me 200 purchases** record hui.  
- Dusri bucket me **2016 ke second half me 180 purchases** hui.  

🔹 **Note:** **from include hota hai, to exclude hota hai**, isliye `"to": "2016-07-01"` ka matlab hai **30th June tak ki purchases**.  

---

## 🎨 **4. Result Formatting aur Custom Keys**  
Elasticsearch **default me buckets ke keys** ko `"from-to"` format me show karta hai.  
Agar hume **custom keys ya specific date format** chahiye, to hum `"format"` aur `"key"` parameters ka use kar sakte hain.  

### ✅ **Custom Formatting Example:**  
```json
{
  "size": 0,
  "aggs": {
    "purchase_ranges": {
      "date_range": {
        "field": "purchased_at",
        "format": "yyyy-MM-dd",
        "keyed": true,
        "ranges": [
          { "key": "first_half", "from": "2016-01-01", "to": "2016-07-01" },
          { "key": "second_half", "from": "2016-07-01", "to": "2017-01-01" }
        ]
      }
    }
  }
}
```

### 📊 **Output:**  
```json
{
  "aggregations": {
    "purchase_ranges": {
      "buckets": {
        "first_half": { "from": "2016-01-01", "to": "2016-07-01", "doc_count": 200 },
        "second_half": { "from": "2016-07-01", "to": "2017-01-01", "doc_count": 180 }
      }
    }
  }
}
```

✅ `"keyed": true` karne se **buckets ke names JSON keys ban jate hain**.  
✅ `"format": "yyyy-MM-dd"` se **dates readable format me aati hain**.  

---

## 📊 **5. Sub-Aggregations: Bucket Statistics**  
Sirf **document counts lena enough nahi hota**, **hume aur details bhi chahiye**.  

💡 **Scenario:**  
Har price range me **total, min, max, avg, aur count** nikalna hai.  

### ✅ **Query:**  
```json
{
  "size": 0,
  "aggs": {
    "amount_distribution": {
      "range": {
        "field": "total_amount",
        "ranges": [
          { "to": 50 },
          { "from": 50, "to": 100 },
          { "from": 100 }
        ]
      },
      "aggs": {
        "stats_per_bucket": {
          "stats": { "field": "total_amount" }
        }
      }
    }
  }
}
```

### 📊 **Output:**  
```json
{
  "aggregations": {
    "amount_distribution": {
      "buckets": [
        { "key": "*-50", "doc_count": 150, "stats_per_bucket": { "min": 10, "max": 49, "avg": 30 } },
        { "key": "50-100", "doc_count": 100, "stats_per_bucket": { "min": 51, "max": 99, "avg": 75 } }
      ]
    }
  }
}
```

---

## 🔄 **6. Recap: Key Takeaways**  
✔ **Range aggregation se numeric values ko predefined buckets me place kar sakte hain.**  
✔ **Date range aggregation se time-based data ko categorize kar sakte hain.**  
✔ **Custom keys aur date formats result readability improve karte hain.**  
✔ **Sub-aggregations se buckets ke andar further statistics calculate kar sakte hain.**  

