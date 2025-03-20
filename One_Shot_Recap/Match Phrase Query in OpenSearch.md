# **Match Phrase Query in OpenSearch**  

## **📌 Introduction**  
**Match Phrase Query** ek **full-text search query** hai jo **exact phrase match** karti hai. Matlab jo words search ho rahe hain, **unko ek hi sequence me match karna zaroori hota hai**.  

🛑 **Difference from Match Query:**  
- **Match Query** → Words **kahin bhi** aaye to match ho sakte hain.  
- **Match Phrase Query** → **Exact sequence** me words hone chahiye.  

---

## **📌 Syntax (Short & Simple)**  
```json
{
  "query": {
    "match_phrase": {
      "field_name": "exact phrase"
    }
  }
}
```
🔹 **field_name** → Jis field me search karna hai.  
🔹 **exact phrase** → Jo **exact sequence** me match honi chahiye.  

---

## **✅ Example**  
Hum ek index **products** banayenge aur uspe **match_phrase query** lagayenge.

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
{ "name": "iPhone 13 Pro Max" }
{ "index": { "_id": 3 } }
{ "name": "Samsung Galaxy S21" }
```

---

### **Step 2: Match Phrase Query Example**
```json
GET products/_search
{
  "query": {
    "match_phrase": {
      "name": "iPhone 13"
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
      { "_id": "1", "_source": { "name": "Apple iPhone 13" } }
    ]
  }
}
```
✔ **"Apple iPhone 13"** match ho gaya kyunki **"iPhone 13" exact sequence me aaya hai**.  
❌ **"iPhone 13 Pro Max" match nahi hua** kyunki "Pro Max" beech me aa raha hai.  

---

## **🆚 Match vs Match Phrase**
| Feature          | Match Query | Match Phrase Query |
|-----------------|------------|--------------------|
| **Partial Match** | ✅ Yes | ❌ No |
| **Word Order Important?** | ❌ No | ✅ Yes |
| **Best for** | Loose searches | Exact phrase searches |

---

## **📌 Key Takeaways**  
✔ **Match Phrase Query** sirf **exact sequence** ko match karti hai.  
✔ **Match Query flexible hoti hai**, lekin **Match Phrase strict** hoti hai.  
✔ **Case-insensitive** hoti hai.  
✔ **Search accuracy badhane ke liye** useful hai jab **exact order maintain karna ho**.  

