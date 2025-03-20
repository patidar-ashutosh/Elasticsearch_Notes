# **📌 Aggregation in OpenSearch**  

## **📌 Introduction**  
Aggregation ka use **data ko group karne, analyze karne, aur summarize karne** ke liye hota hai.  
- OpenSearch me aggregations queries ki tarah kaam karti hain, lekin yeh **documents return nahi karti**, balki **statistics ya grouped results** provide karti hain.  
- Aggregations ka use **data visualization, analytics, aur reporting** me hota hai.  

📌 **Use Cases:**  
- **Total number of documents count karna**  
- **Price ka average, max, min nikalna**  
- **Data ko categories me group karna** (jaise product categories)  
- **Date-wise analysis karna** (jaise monthly sales)  

---

## **📌 Syntax (Short & Simple)**  
```json
GET index_name/_search
{
  "size": 0,
  "aggs": {
    "aggregation_name": {
      "aggregation_type": {
        "field": "field_name"
      }
    }
  }
}
```
🔹 **index_name** → Jis index me search karna hai.  
🔹 **size: 0** → Kyunki aggregations sirf summary provide karti hai, documents return nahi hote.  
🔹 **aggs** → Aggregation ka parent object.  
🔹 **aggregation_name** → Aggregation ka naam.  
🔹 **aggregation_type** → Kis type ki aggregation karni hai (jaise sum, avg, terms, etc.).  
🔹 **field_name** → Jis field pe aggregation apply karni hai.  

---

## **✅ Example**  
Hum ek index **sales** banayenge aur aggregations use karenge.

### **Step 1: Index & Data Creation**
```json
PUT sales
{
  "mappings": {
    "properties": {
      "product": { "type": "keyword" },
      "price": { "type": "integer" },
      "quantity": { "type": "integer" },
      "date": { "type": "date" }
    }
  }
}
```

```json
POST sales/_bulk
{ "index": { "_id": 1 } }
{ "product": "Laptop", "price": 800, "quantity": 2, "date": "2024-03-01" }
{ "index": { "_id": 2 } }
{ "product": "Phone", "price": 500, "quantity": 5, "date": "2024-03-02" }
{ "index": { "_id": 3 } }
{ "product": "Laptop", "price": 850, "quantity": 1, "date": "2024-03-03" }
{ "index": { "_id": 4 } }
{ "product": "Tablet", "price": 300, "quantity": 3, "date": "2024-03-04" }
```

---

## **✅ Example 1: Total Sales Count (`count` aggregation)**
```json
GET sales/_search
{
  "size": 0,
  "aggs": {
    "total_sales": {
      "value_count": {
        "field": "price"
      }
    }
  }
}
```
### **🟢 Expected Output**
```json
{
  "aggregations": {
    "total_sales": {
      "value": 4
    }
  }
}
```
✔ **Total 4 sales hui hain.**  

---

## **✅ Example 2: Maximum Price (`max` aggregation)**
```json
GET sales/_search
{
  "size": 0,
  "aggs": {
    "max_price": {
      "max": {
        "field": "price"
      }
    }
  }
}
```
### **🟢 Expected Output**
```json
{
  "aggregations": {
    "max_price": {
      "value": 850
    }
  }
}
```
✔ **Sabse mehenga product ka price `850` hai.**  

---

## **✅ Example 3: Average Price (`avg` aggregation)**
```json
GET sales/_search
{
  "size": 0,
  "aggs": {
    "average_price": {
      "avg": {
        "field": "price"
      }
    }
  }
}
```
✔ **Average price calculate hoke return hoga.**  

---

## **✅ Example 4: Product-wise Sales (`terms` aggregation)**
```json
GET sales/_search
{
  "size": 0,
  "aggs": {
    "sales_by_product": {
      "terms": {
        "field": "product"
      }
    }
  }
}
```
### **🟢 Expected Output**
```json
{
  "aggregations": {
    "sales_by_product": {
      "buckets": [
        { "key": "Laptop", "doc_count": 2 },
        { "key": "Phone", "doc_count": 1 },
        { "key": "Tablet", "doc_count": 1 }
      ]
    }
  }
}
```
✔ **Laptop ki 2 sales, Phone aur Tablet ki 1-1 sales hain.**  

---

## **✅ Example 5: Date-wise Sales (`date_histogram` aggregation)**
```json
GET sales/_search
{
  "size": 0,
  "aggs": {
    "sales_over_time": {
      "date_histogram": {
        "field": "date",
        "calendar_interval": "day"
      }
    }
  }
}
```
✔ **Yeh aggregation daily sales count karegi.**  

---

## **📌 Key Takeaways**  
✔ **Aggregations data ko summarize aur analyze karne ke liye hoti hain.**  
✔ **Basic types: `count`, `sum`, `avg`, `min`, `max`**  
✔ **Group-wise analysis ke liye `terms` aggregation use hoti hai.**  
✔ **Date-based analysis ke liye `date_histogram` aggregation best hai.**  
✔ **Aggregations search results return nahi karti, sirf summary deti hain.**  

