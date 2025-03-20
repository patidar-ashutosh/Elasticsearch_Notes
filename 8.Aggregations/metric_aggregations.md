**Metric Aggregations numeric values** ko process karne ke liye hoti hain, aur inhe **do categories** me divide kiya gaya hai:  

1️⃣ **Single-Value Metric Aggregations** ➝ Ye sirf ek value return karti hain. (e.g., `sum`, `avg`, `min`, `max`)  
2️⃣ **Multi-Value Metric Aggregations** ➝ Ye multiple values return karti hain. (e.g., `stats`, `extended_stats`, `percentiles`)  

---

## 📜 **Table of Contents**
1️⃣ [Introduction to Metric Aggregations](#1)  

2️⃣ [Single-Value Metric Aggregations](#2)  
   - 🔢 [Sum Aggregation](#2.1)  
   - 📊 [Average Aggregation](#2.2)  
   - 📉 [Min Aggregation](#2.3)  
   - 📈 [Max Aggregation](#2.4)  
   - 🔢 [Value Count Aggregation](#2.5)  

3️⃣ [Multi-Value Metric Aggregations](#3)  
   - 📊 [Stats Aggregation](#3.1)  
   - 📊 [Extended Stats Aggregation](#3.2)  
   - 🎯 [Cardinality Aggregation](#3.3)  
   
4️⃣ [Practical Example with Query](#4)  

---

## 1️⃣ 🎯 **Introduction to Metric Aggregations** <a name="1"></a>
Metric aggregations Elasticsearch me **numeric fields** pe aggregations perform karne ke liye hoti hain. Ye aggregations search queries ke results ya **poore index ke documents** pe apply ho sakti hain.  

Jaise agar ek `orders` index me **total_amount** field hai, to hum `sum`, `avg`, `min`, `max`, `count` ya `stats` aggregation apply karke different calculations nikal sakte hain.  

**Example Use Case:**  
- **E-commerce**: Monthly sales ka total (`sum`), average order value (`avg`), sabse expensive (`max`), sabse sasta (`min`), total orders (`value_count`)  
- **Analytics**: Users ka unique count (`cardinality`), Statistical data (`stats`, `extended_stats`)  

---

## 2️⃣ 🔢 **Single-Value Metric Aggregations** <a name="2"></a>

### 2.1 🔢 **Sum Aggregation** <a name="2.1"></a>
Yeh aggregation ek numeric field ke **sum (योग)** ko return karti hai.  

🔹 **Use Case:** Total sales calculate karna.  
🔹 **Example Query:**
```json
GET orders/_search
{
  "size": 0,
  "aggs": {
    "total_sales": {
      "sum": {
        "field": "total_amount"
      }
    }
  }
}
```
🔹 **Expected Output:**
```json
{
  "aggregations": {
    "total_sales": {
      "value": 250000.0
    }
  }
}
```
👉 Matlab, **total orders ka amount ₹250,000 hai.**  

---

### 2.2 📊 **Average Aggregation** <a name="2.2"></a>
Yeh aggregation ek numeric field ka **average (औसत)** calculate karti hai.  

🔹 **Use Case:** Average order value nikalna.  
🔹 **Example Query:**
```json
GET orders/_search
{
  "size": 0,
  "aggs": {
    "avg_order_value": {
      "avg": {
        "field": "total_amount"
      }
    }
  }
}
```
🔹 **Expected Output:**
```json
{
  "aggregations": {
    "avg_order_value": {
      "value": 2500.0
    }
  }
}
```
👉 Matlab, **har order ka average ₹2500 hai.**  

---

### 2.3 📉 **Min Aggregation** <a name="2.3"></a>
Yeh aggregation ek numeric field ka **sabse chhota (minimum) value** return karti hai.  

🔹 **Use Case:** Sabse sasta order ka amount nikalna.  
🔹 **Example Query:**
```json
GET orders/_search
{
  "size": 0,
  "aggs": {
    "min_order_value": {
      "min": {
        "field": "total_amount"
      }
    }
  }
}
```
🔹 **Expected Output:**
```json
{
  "aggregations": {
    "min_order_value": {
      "value": 100.0
    }
  }
}
```
👉 Matlab, **sabse chhota order ₹100 ka hai.**  

---

### 2.4 📈 **Max Aggregation** <a name="2.4"></a>
Yeh aggregation ek numeric field ka **sabse bada (maximum) value** return karti hai.  

🔹 **Use Case:** Sabse expensive order ka amount nikalna.  
🔹 **Example Query:**
```json
GET orders/_search
{
  "size": 0,
  "aggs": {
    "max_order_value": {
      "max": {
        "field": "total_amount"
      }
    }
  }
}
```
🔹 **Expected Output:**
```json
{
  "aggregations": {
    "max_order_value": {
      "value": 50000.0
    }
  }
}
```
👉 Matlab, **sabse expensive order ₹50,000 ka hai.**  

---

### 2.5 🔢 **Value Count Aggregation** <a name="2.5"></a>
Yeh aggregation ek numeric field ke **total count** ko return karti hai.  

🔹 **Use Case:** Total number of orders count karna.  
🔹 **Example Query:**
```json
GET orders/_search
{
  "size": 0,
  "aggs": {
    "total_orders": {
      "value_count": {
        "field": "total_amount"
      }
    }
  }
}
```
🔹 **Expected Output:**
```json
{
  "aggregations": {
    "total_orders": {
      "value": 1000
    }
  }
}
```
👉 Matlab, **total 1000 orders hain.**  

---

## 3️⃣ 📊 **Multi-Value Metric Aggregations** <a name="3"></a>

### 3.1 📊 **Stats Aggregation** <a name="3.1"></a>
Yeh ek **combined aggregation** hai jo **sum, avg, min, max, aur count** ek sath return karti hai.  

🔹 **Use Case:** Ek hi query me multiple stats chahiye ho.  
🔹 **Example Query:**
```json
GET orders/_search
{
  "size": 0,
  "aggs": {
    "amount_stats": {
      "stats": {
        "field": "total_amount"
      }
    }
  }
}
```
🔹 **Expected Output:**
```json
{
  "aggregations": {
    "amount_stats": {
      "count": 1000,
      "min": 100.0,
      "max": 50000.0,
      "avg": 2500.0,
      "sum": 250000.0
    }
  }
}
```
👉 Matlab, **yeh ek combined output hai jo alag-alag queries se bacha sakta hai.**  

---

### 3.3 🎯 **Cardinality Aggregation** <a name="3.3"></a>
Yeh aggregation **unique values** count karti hai.  

🔹 **Use Case:** Kitne unique salesmen hain count karna.  
🔹 **Example Query:**
```json
GET orders/_search
{
  "size": 0,
  "aggs": {
    "unique_salesmen": {
      "cardinality": {
        "field": "salesman.id"
      }
    }
  }
}
```
🔹 **Expected Output:**
```json
{
  "aggregations": {
    "unique_salesmen": {
      "value": 100
    }
  }
}
```
👉 Matlab, **total 100 unique salesmen hain.**  

---

