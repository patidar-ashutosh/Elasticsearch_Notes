# **Limit Query in OpenSearch**  

## **📌 Introduction**  
**Limit Query** ka use **maximum number of documents restrict karne** ke liye hota hai.  
- Yeh **query context** me use hoti hai.  
- Yeh sirf **maximum document count** control karti hai, **sorting ya relevance pe effect nahi dalti**.  
- `size` se different hai kyunki `size` search ke **total hits** control karta hai, jabki `limit` sirf **filtered documents pe apply hota hai**.  

📌 **Use Case:**  
Agar tumhe kisi **specific condition** ke saath **fixed number of documents retrieve** karne hain, to **limit query** ka use kar sakte ho.  

---

## **📌 Syntax (Short & Simple)**  
```json
GET index_name/_search
{
  "query": {
    "bool": {
      "must": [
        { "match_all": {} }
      ],
      "filter": {
        "limit": { "value": number }
      }
    }
  }
}
```
🔹 **index_name** → Jis index me search karna hai.  
🔹 **value** → Maximum **documents ka limit** (default koi limit nahi hoti).  

---

## **✅ Example**  
Hum ek index **products** banayenge aur uspe **limit query** lagayenge.

### **Step 1: Index & Data Creation**
```json
PUT products
{
  "mappings": {
    "properties": {
      "name": { "type": "text" }
    }
  }
}
```

```json
POST products/_bulk
{ "index": { "_id": 1 } }
{ "name": "iPhone 13" }
{ "index": { "_id": 2 } }
{ "name": "Samsung Galaxy S21" }
{ "index": { "_id": 3 } }
{ "name": "OnePlus 9" }
{ "index": { "_id": 4 } }
{ "name": "Google Pixel 6" }
{ "index": { "_id": 5 } }
{ "name": "Sony Xperia 5" }
```

---

### **Step 2: Default Query (Without Limit)**
```json
GET products/_search
{
  "query": {
    "match_all": {}
  }
}
```
🔹 **Default me OpenSearch 10 results return karega (ya jitne bhi documents hain).**  

---

### **Step 3: Limit Query Example**
```json
GET products/_search
{
  "query": {
    "bool": {
      "must": [
        { "match_all": {} }
      ],
      "filter": {
        "limit": { "value": 3 }
      }
    }
  }
}
```

### **🟢 Expected Output**
```json
{
  "hits": {
    "total": { "value": 5 },
    "hits": [
      { "_id": "1", "_source": { "name": "iPhone 13" } },
      { "_id": "2", "_source": { "name": "Samsung Galaxy S21" } },
      { "_id": "3", "_source": { "name": "OnePlus 9" } }
    ]
  }
}
```
✔ **Sirf 3 results return honge kyunki `limit: 3` set kiya hai.**  
✔ **Filtering hone ke baad limit apply hoti hai.**  

---

## **🆚 Difference Between `size` and `limit`**
| Feature         | `size` | `limit` |
|---------------|---------|--------|
| **Use Case** | Total search results control karta hai | Filtered results ko restrict karta hai |
| **Query Context?** | ✅ Yes | ❌ No (Filter context me hota hai) |
| **Affect Sorting?** | ✅ Yes | ❌ No |
| **Performance Impact?** | High (Sorting impact karega) | Low (Sirf filter hone ke baad apply hota hai) |

---

## **📌 Key Takeaways**  
✔ **Limit Query filtered documents pe apply hoti hai, size pure search pe.**  
✔ **Pagination ke liye size use karo, filtering ke liye limit.**  
✔ **Performance optimize karne ke liye useful hai.**  

