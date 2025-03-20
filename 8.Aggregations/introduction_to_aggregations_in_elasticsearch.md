# 📌 Introduction to Aggregations in **Elasticsearch**  

## 📑 **Table of Contents**  
1. 🔍 **Introduction** – Aggregations Kya Hain?  
2. 📊 **Why Aggregations?** – Use Cases  
3. 🏗 **Understanding Execution Context**  
4. 🎯 **Basic Example: Grouping Orders by Product ID**  
5. 🛠 **Important Fields in Our Test Data**  
6. 🔢 **Types of Aggregations in Elasticsearch**  
7. ✅ **Next Steps & Best Practices**  

---

## 🔍 **1. Introduction – Aggregations Kya Hain?**  
Elasticsearch me **aggregations** ek **powerful feature** hai jo tumhe data ko group karne, analyze karne, aur statistical summaries nikalne ki ability deti hai.  

Agar tum SQL se familiar ho, toh tumhe `GROUP BY`, `SUM()`, `AVG()`, `COUNT()` jaise functions pata honge. **Elasticsearch aggregations usse bhi zyada advanced aur flexible hain!**  

✔ **Aggregations se tum complex queries likh sakte ho** jaise:  
- **Kitna total revenue generate hua hai?**  
- **Kaunsa product sabse zyada bik raha hai?**  
- **Ek salesman ne kitne orders complete kiye hain?**  

---

## 📊 **2. Why Aggregations? – Use Cases**  
Aggregations tumhare **big data analytics** ke liye bahut useful hain. Kuch real-world scenarios dekho:  

| 💡 **Use Case** | 🎯 **Aggregation Type** |
|---------------|-------------------|
| Top 5 best-selling products | **Terms Aggregation** (group by product ID) |
| Total revenue per month | **Date Histogram Aggregation** |
| Average order amount | **Avg Aggregation** |
| Total sales by each salesman | **Sum Aggregation** |
| Count of orders per status | **Terms Aggregation** |

---

## 🏗 **3. Understanding Execution Context**  
Aggregation queries **document set** pe kaam karti hain, jo **execution context** ke through define hota hai.  

🚀 **Example:**  
- Tum **orders index** se sirf **completed orders** ka total revenue nikalna chahte ho.  
- To tum **status: "completed"** pe filter lagaoge.  
- Aggregation sirf **unhi documents pe chalega jo filter ke baad bache hain.**  

---

## 🎯 **4. Basic Example: Grouping Orders by Product ID**  
Samjho ki tumhare paas **orders index** hai aur tum har **product ID** ke basis pe **total amount** dekhna chahte ho.  

### ✅ **Query: Sum of Sales per Product**
```json
{
  "size": 0,
  "aggs": {
    "total_sales_per_product": {
      "terms": { "field": "lines.product_id.keyword" },
      "aggs": {
        "total_amount": { "sum": { "field": "lines.amount" } }
      }
    }
  }
}
```
🔍 **Explanation:**  
- `"size": 0` → Documents return nahi karne, sirf aggregation ka result chahiye.  
- `"terms"` → Products ko group karega.  
- `"sum"` → Har product ke total amount ka sum karega.  

### 📊 **Expected Output:**
```json
{
  "aggregations": {
    "total_sales_per_product": {
      "buckets": [
        { "key": "Product_A", "doc_count": 120, "total_amount": { "value": 50000 } },
        { "key": "Product_B", "doc_count": 85, "total_amount": { "value": 35000 } }
      ]
    }
  }
}
```
✅ **Product A ka total sale ₹50,000 aur Product B ka ₹35,000 hai!**  

---

## 🛠 **5. Important Fields in Our Test Data**  
Test data me **orders index** ka structure kuch aisa hai:  

| 🏷 **Field** | 🔍 **Description** |
|-------------|-------------------|
| **lines** | Ek **nested object** jo multiple items store karta hai |
| **lines.amount** | Order line ka price |
| **lines.product_id** | Product ka unique ID |
| **lines.quantity** | Ordered quantity |
| **purchased_at** | Order ka timestamp |
| **sales_channel** | Kis channel se order aaya (Online, Store, etc.) |
| **salesman.id** | Salesman ka ID |
| **salesman.name** | Salesman ka naam |
| **status** | Order ka status (`pending`, `completed`, etc.) |
| **total_amount** | Poore order ka total |

💡 **Nested Fields**: `lines` ek array hai jo **nested objects** store karta hai. Aggregations me isse handle karne ke liye **nested aggregations** use karni padengi.

---

## 🔢 **6. Types of Aggregations in Elasticsearch**  
Aggregations kaafi **categories me divided** hoti hain. Tumhe **Metric Aggregations** se start karna chahiye.

### 🔹 **Metric Aggregations (Statistics ke liye)**  
| 📌 **Aggregation** | 🎯 **Use Case** |
|-----------------|-----------------|
| **Sum** | Total sales ka sum nikalna |
| **Avg** | Average order amount |
| **Min** | Smallest order value |
| **Max** | Largest order value |
| **Count** | Total number of orders |

✅ **Example: Average Order Amount**
```json
{
  "size": 0,
  "aggs": {
    "average_order_amount": { "avg": { "field": "total_amount" } }
  }
}
```

---

### 🔹 **Bucket Aggregations (Grouping ke liye)**  
| 📌 **Aggregation** | 🎯 **Use Case** |
|-----------------|-----------------|
| **Terms** | Top selling products |
| **Histogram** | Sales distribution per price range |
| **Date Histogram** | Monthly revenue analysis |
| **Range** | Orders categorized by price range |

✅ **Example: Orders Grouped by Status**
```json
{
  "size": 0,
  "aggs": {
    "orders_by_status": {
      "terms": { "field": "status.keyword" }
    }
  }
}
```

---

### 🔹 **Nested Aggregations (Complex Objects ke liye)**  
Agar tumhe **nested objects** ke andar aggregations chalani hain, toh tumhe **nested aggregations** ka use karna padega.

✅ **Example: Sales by Product ID (Nested)**
```json
{
  "size": 0,
  "aggs": {
    "sales_per_product": {
      "nested": { "path": "lines" },
      "aggs": {
        "products": {
          "terms": { "field": "lines.product_id.keyword" },
          "aggs": {
            "total_sales": { "sum": { "field": "lines.amount" } }
          }
        }
      }
    }
  }
}
```

---

## ✅ **7. Next Steps & Best Practices**  
✔ **Aggregation queries ko optimize karo** `size: 0` use karke taaki unnecessary documents return na ho.  
✔ **Fields ko keyword type me store karo** agar tum `terms` aggregation use kar rahe ho.  
✔ **Nested aggregations ka dhyan rakho** agar tumhare data me objects ke andar objects hain.  
✔ **Pipeline aggregations** explore karo complex calculations ke liye.  

---

## 🚀 **What’s Next?**  
💡 **Next topic me hum Metric Aggregations ko detail me dekhenge!**  
