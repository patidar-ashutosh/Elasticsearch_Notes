# **_source in OpenSearch**  

## **📌 Introduction**  
`_source` ek special field hai jo OpenSearch me **document ke actual stored data** ko represent karta hai.  
- Jab tum query chalate ho, to OpenSearch **by default** `_source` field return karta hai.  
- Tum **specific fields retrieve** karne ke liye `_source` ko customize kar sakte ho.  
- `_source` ko **disable** bhi kiya ja sakta hai agar tumhe sirf indexing chahiye, data retrieval nahi.  

📌 **Use Cases:**  
- **Sirf kuch specific fields retrieve karna** (e.g., sirf `name` field chahiye, pura document nahi).  
- **Security reasons** ke liye kuch sensitive fields hide karna.  
- **Performance optimize** karne ke liye sirf required fields lena.  

---

## **📌 Syntax (Short & Simple)**  
```json
GET index_name/_search
{
  "_source": ["field1", "field2"],
  "query": {
    "match_all": {}
  }
}
```
🔹 **index_name** → Jis index me search karna hai.  
🔹 **_source** → Sirf specified fields return karne ke liye.  

---

## **✅ Example**  
Hum ek index **products** banayenge aur uspe `_source` ka use karenge.

### **Step 1: Index & Data Creation**
```json
PUT products
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "price": { "type": "integer" },
      "category": { "type": "text" }
    }
  }
}
```

```json
POST products/_bulk
{ "index": { "_id": 1 } }
{ "name": "iPhone 13", "price": 799, "category": "Mobile" }
{ "index": { "_id": 2 } }
{ "name": "Samsung Galaxy S21", "price": 699, "category": "Mobile" }
{ "index": { "_id": 3 } }
{ "name": "Sony WH-1000XM4", "price": 349, "category": "Headphones" }
```

---

### **Step 2: Default Query (Full `_source` Returned)**
```json
GET products/_search
{
  "query": {
    "match_all": {}
  }
}
```

### **🟢 Expected Output**
```json
{
  "hits": {
    "hits": [
      { "_id": "1", "_source": { "name": "iPhone 13", "price": 799, "category": "Mobile" } },
      { "_id": "2", "_source": { "name": "Samsung Galaxy S21", "price": 699, "category": "Mobile" } },
      { "_id": "3", "_source": { "name": "Sony WH-1000XM4", "price": 349, "category": "Headphones" } }
    ]
  }
}
```
✔ **By default, pura `_source` return hota hai.**  

---

### **Step 3: Select Specific Fields Using `_source`**
Agar tumhe sirf `name` aur `price` chahiye, to `_source` specify kar sakte ho:  
```json
GET products/_search
{
  "_source": ["name", "price"],
  "query": {
    "match_all": {}
  }
}
```

### **🟢 Expected Output**
```json
{
  "hits": {
    "hits": [
      { "_id": "1", "_source": { "name": "iPhone 13", "price": 799 } },
      { "_id": "2", "_source": { "name": "Samsung Galaxy S21", "price": 699 } },
      { "_id": "3", "_source": { "name": "Sony WH-1000XM4", "price": 349 } }
    ]
  }
}
```
✔ **Sirf `name` aur `price` return ho raha hai, `category` nahi.**  

---

### **Step 4: Exclude Fields from `_source`**
Agar tumhe **kuch fields hide** karni hain, to `_source` me `excludes` ka use kar sakte ho:  
```json
GET products/_search
{
  "_source": {
    "excludes": ["price"]
  },
  "query": {
    "match_all": {}
  }
}
```

### **🟢 Expected Output**
```json
{
  "hits": {
    "hits": [
      { "_id": "1", "_source": { "name": "iPhone 13", "category": "Mobile" } },
      { "_id": "2", "_source": { "name": "Samsung Galaxy S21", "category": "Mobile" } },
      { "_id": "3", "_source": { "name": "Sony WH-1000XM4", "category": "Headphones" } }
    ]
  }
}
```
✔ **`price` field return nahi ho rahi.**  

---

## **🔹 Disable `_source` Completely**
Agar tumhe indexing ke time hi `_source` disable karna hai, to index create karte waqt ye setting apply karo:

```json
PUT products
{
  "mappings": {
    "_source": { "enabled": false },
    "properties": {
      "name": { "type": "text" },
      "price": { "type": "integer" }
    }
  }
}
```
✔ **Ab queries me `_source` return nahi hoga, sirf `_id` milega.**  

---

## **📌 Key Takeaways**  
✔ **_source actual stored document hai jo search results me return hota hai.**  
✔ **Sirf kuch fields retrieve karne ke liye `_source` specify kar sakte ho.**  
✔ **Sensitive fields hide karne ke liye `_source` me `excludes` ka use kar sakte ho.**  
✔ **Performance optimize karne ke liye `_source` disable bhi kiya ja sakta hai.**  

