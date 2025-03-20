### **Terms Query in OpenSearch**  

#### 🔹 **Introduction**  
**Terms Query** ek **multiple values match** karne wali query hai. Ye **term query** ka advanced version hai jo ek saath **multiple exact values** ko match karta hai.  

---

## **📌 Syntax (Short & Simple)**  
```json
{
  "query": {
    "terms": {
      "field_name": [ "value1", "value2", "value3" ]
    }
  }
}
```
🔹 **field_name** → Jis field me search karna hai.  
🔹 **value1, value2, value3** → Multiple exact values jo match karni hai.  

---

## **✅ Example**
Hum ek index **products** banayenge aur uspe query lagayenge.

### **Step 1: Index & Data Creation**
```json
PUT products
{
  "mappings": {
    "properties": {
      "name": { "type": "keyword" },
      "category": { "type": "keyword" }
    }
  }
}
```

```json
POST products/_bulk
{ "index": { "_id": 1 } }
{ "name": "iPhone 13", "category": "Electronics" }
{ "index": { "_id": 2 } }
{ "name": "Samsung Galaxy S21", "category": "Electronics" }
{ "index": { "_id": 3 } }
{ "name": "Sony Headphones", "category": "Accessories" }
```

---

### **Step 2: Terms Query Example**
```json
GET products/_search
{
  "query": {
    "terms": {
      "name": [ "iPhone 13", "Sony Headphones" ]
    }
  }
}
```
### **🟢 Expected Output**
```json
{
  "hits": {
    "total": { "value": 2 },
    "hits": [
      { "_id": "1", "_source": { "name": "iPhone 13", "category": "Electronics" } },
      { "_id": "3", "_source": { "name": "Sony Headphones", "category": "Accessories" } }
    ]
  }
}
```
✔ Is query se `"iPhone 13"` aur `"Sony Headphones"` dono match ho gaye.  

---

## **Key Takeaways**
✔ **Terms Query** ek saath multiple values ko match karta hai.  
✔ **Case-sensitive hota hai**, `"iphone 13"` alag treat hoga.  
✔ **Keyword ya Numeric Fields** pe use hota hai.  
✔ **IDs search karne ke liye bhi useful hai.**  

