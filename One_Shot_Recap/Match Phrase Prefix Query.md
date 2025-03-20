# **Match Phrase Prefix Query in OpenSearch**  

## **📌 Introduction**  
**Match Phrase Prefix Query** ek **combination** hai **Match Phrase** aur **Prefix Query** ka.  
- **Match Phrase** ki tarah **sequence maintain karta hai**.  
- **Prefix Query** ki tarah **last word ka prefix match hota hai**.  

📌 **Use Case:**  
Agar tumhe kisi **sentence ke starting words exact match karne hain**, lekin **last word ka partial match allow karna hai**, tab ye query kaam aayegi.  

---

## **📌 Syntax (Short & Simple)**  
```json
{
  "query": {
    "match_phrase_prefix": {
      "field_name": "starting phrase"
    }
  }
}
```
🔹 **field_name** → Jis field me search karna hai.  
🔹 **starting phrase** → Jo **exact order** me hona chahiye, lekin **last word ka prefix match allowed** hai.  

---

## **✅ Example**  
Hum ek index **products** banayenge aur uspe **match_phrase_prefix query** lagayenge.

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
{ "name": "Apple iPhone 13" }
{ "index": { "_id": 2 } }
{ "name": "Apple iPhone 14 Pro" }
{ "index": { "_id": 3 } }
{ "name": "Apple iPad Air" }
{ "index": { "_id": 4 } }
{ "name": "Samsung Galaxy S21" }
```

---

### **Step 2: Match Phrase Prefix Query Example**
```json
GET products/_search
{
  "query": {
    "match_phrase_prefix": {
      "name": "Apple iPh"
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
      { "_id": "1", "_source": { "name": "Apple iPhone 13" } },
      { "_id": "2", "_source": { "name": "Apple iPhone 14 Pro" } }
    ]
  }
}
```
✔ **"Apple iPhone 13" aur "Apple iPhone 14 Pro" match ho gaye kyunki "Apple iPh" se shuru ho raha hai.**  
❌ **"Apple iPad Air" match nahi hoga kyunki "iPh" nahi aata usme.**  

---

## **🆚 Match Phrase vs Match Phrase Prefix**
| Feature         | Match Phrase Query | Match Phrase Prefix Query |
|---------------|------------------|----------------------|
| **Word Order Important?** | ✅ Yes | ✅ Yes |
| **Partial Match?** | ❌ No | ✅ Yes (only last word) |
| **Best for** | Exact phrase search | Auto-suggestion & prefix-based search |

---

## **📌 Key Takeaways**  
✔ **Match Phrase Prefix Query** **sequence maintain karti hai**.  
✔ **Last word ka prefix match allowed hota hai.**  
✔ **Auto-complete suggestions ya type-ahead search ke liye best hai.**  
✔ **Full-text search ka part hai, case-insensitive hoti hai.**  

