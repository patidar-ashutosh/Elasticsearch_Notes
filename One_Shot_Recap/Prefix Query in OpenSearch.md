# **Prefix Query in OpenSearch**  

## **📌 Introduction**  
**Prefix Query** ek **wildcard search** ka simple version hai jo kisi field ke **starting letters** match karne ke liye use hota hai. Matlab agar kisi field ka value kisi specific prefix se shuru ho raha hai, to woh match ho jayega.  

🛑 **Note:**  
- **Keyword & Text dono fields pe kaam karta hai**.  
- **Case-sensitive hota hai** agar field ka type `keyword` hai.  

---

## **📌 Syntax (Short & Simple)**  
```json
{
  "query": {
    "prefix": {
      "field_name": {
        "value": "starting_text"
      }
    }
  }
}
```
🔹 **field_name** → Jis field me search karna hai.  
🔹 **starting_text** → Jo **prefix (starting letters)** match karni hai.  

---

## **✅ Example**  
Hum ek index **products** banayenge aur uspe **prefix query** lagayenge.

### **Step 1: Index & Data Creation**
```json
PUT products
{
  "mappings": {
    "properties": {
      "name": { "type": "keyword" }
    }
  }
}
```

```json
POST products/_bulk
{ "index": { "_id": 1 } }
{ "name": "iPhone 13" }
{ "index": { "_id": 2 } }
{ "name": "iPhone 14 Pro" }
{ "index": { "_id": 3 } }
{ "name": "Samsung Galaxy S21" }
```

---

### **Step 2: Prefix Query Example**
```json
GET products/_search
{
  "query": {
    "prefix": {
      "name": {
        "value": "iPhone 1"
      }
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
      { "_id": "1", "_source": { "name": "iPhone 13" } },
      { "_id": "2", "_source": { "name": "iPhone 14 Pro" } }
    ]
  }
}
```
✔ **"iPhone 13" aur "iPhone 14 Pro" match ho gaye kyunki dono "iPhone 1" se shuru hote hain.**  
❌ **"Samsung Galaxy S21" match nahi hoga.**  

---

## **🆚 Term vs Match vs Prefix**
| Feature         | Term Query | Match Query | Prefix Query |
|---------------|-----------|------------|-------------|
| **Exact Match** | ✅ Yes | ❌ No | ❌ No |
| **Partial Match** | ❌ No | ✅ Yes | ✅ Yes (only start) |
| **Case Sensitive?** | ✅ Yes | ❌ No | ✅ Yes (for keyword fields) |
| **Best for** | Exact values (IDs, categories) | Full-text search | Word starting search |

---

## **📌 Key Takeaways**  
✔ **Prefix Query starting characters match karta hai.**  
✔ **Keyword fields pe case-sensitive hota hai.**  
✔ **Full-text search ke liye nahi, sirf prefix-based search ke liye useful hai.**  
✔ **Large datasets pe slow ho sakta hai, toh careful use karo.**  

