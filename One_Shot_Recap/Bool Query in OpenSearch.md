# **Bool Query in OpenSearch**  

## **📌 Introduction**  
**Bool Query** ek **combination query** hai jo multiple queries ko combine karti hai. Isme **must, must_not, should, filter** clauses hote hain jo alag-alag conditions define karne ke liye use hote hain.  

---

## **📌 Syntax (Short & Simple)**  
```json
{
  "query": {
    "bool": {
      "must": [ { QUERY_1 }, { QUERY_2 } ],
      "must_not": [ { QUERY_3 } ],
      "should": [ { QUERY_4 } ],
      "filter": [ { QUERY_5 } ]
    }
  }
}
```
🔹 **must** → Yeh queries **match honi chahiye** (AND condition).  
🔹 **must_not** → Yeh queries **match nahi honi chahiye** (NOT condition).  
🔹 **should** → Yeh queries **optional hain**, lekin agar match hoti hain to score badhta hai (OR condition).  
🔹 **filter** → Must jaisa hi hai, **lekin score affect nahi karta** (faster search).  

---

## **✅ Example**
Hum ek index **products** banayenge aur uspe **bool query** lagayenge.

### **Step 1: Index & Data Creation**
```json
PUT products
{
  "mappings": {
    "properties": {
      "name": { "type": "keyword" },
      "category": { "type": "keyword" },
      "price": { "type": "double" }
    }
  }
}
```

```json
POST products/_bulk
{ "index": { "_id": 1 } }
{ "name": "iPhone 13", "category": "Electronics", "price": 799.99 }
{ "index": { "_id": 2 } }
{ "name": "Samsung Galaxy S21", "category": "Electronics", "price": 699.99 }
{ "index": { "_id": 3 } }
{ "name": "Sony Headphones", "category": "Accessories", "price": 199.99 }
```

---

### **Step 2: Bool Query Example**
```json
GET products/_search
{
  "query": {
    "bool": {
      "must": [
        { "term": { "category": "Electronics" } }
      ],
      "must_not": [
        { "term": { "name": "Samsung Galaxy S21" } }
      ],
      "should": [
        { "range": { "price": { "lt": 800 } } }
      ],
      "filter": [
        { "range": { "price": { "gt": 500 } } }
      ]
    }
  }
}
```

---

### **🟢 Expected Output**
```json
{
  "hits": {
    "total": { "value": 1 },
    "hits": [
      { "_id": "1", "_source": { "name": "iPhone 13", "category": "Electronics", "price": 799.99 } }
    ]
  }
}
```

---

## **Key Takeaways**
✔ **Bool Query** multiple queries ko combine karne ke liye use hoti hai.  
✔ **must** → Query **match honi chahiye** (AND condition).  
✔ **must_not** → Query **match nahi honi chahiye** (NOT condition).  
✔ **should** → Query **optional hai**, but match hone par score badhta hai (OR condition).  
✔ **filter** → Query **fast hoti hai**, but score affect nahi karti.  

