# **Match Query in OpenSearch**  

## **📌 Introduction**  
**Match Query** ek **full-text search** query hai jo **analysis process** (tokenization, stemming, lowercasing, etc.) ka use karke text ko search karti hai. Ye **case-insensitive** hoti hai aur partial matching bhi support karti hai.  

---

## **📌 Syntax (Short & Simple)**  
```json
{
  "query": {
    "match": {
      "field_name": "search_text"
    }
  }
}
```
🔹 **field_name** → Jis field me search karna hai.  
🔹 **search_text** → Jo text search karna hai (case-insensitive).  

---

## **✅ Example**
Hum ek index **products** banayenge aur uspe **match query** lagayenge.

### **Step 1: Index & Data Creation**
```json
PUT products
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "category": { "type": "text" }
    }
  }
}
```

```json
POST products/_bulk
{ "index": { "_id": 1 } }
{ "name": "iPhone 13", "category": "Smartphones" }
{ "index": { "_id": 2 } }
{ "name": "Samsung Galaxy S21", "category": "Smartphones" }
{ "index": { "_id": 3 } }
{ "name": "Sony Headphones", "category": "Accessories" }
```

---

### **Step 2: Match Query Example**
```json
GET products/_search
{
  "query": {
    "match": {
      "name": "iphone"
    }
  }
}
```

### **🟢 Expected Output**
```json
{
  "hits": {
    "total": { "value": 1 },
    "hits": [
      { "_id": "1", "_source": { "name": "iPhone 13", "category": "Smartphones" } }
    ]
  }
}
```
✔ **"iPhone 13"** match ho gaya kyunki **match query case-insensitive hoti hai**.  

---

## **🆚 Term Query vs Match Query**
| Feature          | Term Query | Match Query |
|-----------------|-----------|------------|
| **Analysis**    | ❌ No     | ✅ Yes |
| **Case-Sensitive** | ✅ Yes | ❌ No |
| **Partial Match** | ❌ No | ✅ Yes |
| **Best for**     | IDs, exact values | Full-text search |

---

## **📌 Key Takeaways**  
✔ **Match Query** **case-insensitive** hoti hai.  
✔ **Full-text search** ke liye **match query** best option hai.  
✔ **Tokenization & stemming** ke wajah se **partial matching** bhi hoti hai.  
✔ **Term query** sirf **exact match** ke liye use hoti hai, **match query** flexible hoti hai.  

