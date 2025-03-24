### **How the "keyword" Data Type Works in Elasticsearch**  

Elasticsearch me `keyword` data type ka main use filtering, sorting, aur aggregations ke liye hota hai. Lekin, iska use full-text search ke liye kyun nahi hota? Iska jawab samajhne ke liye humein ye dekhna padega ki `keyword` fields ko kaise analyze aur store kiya jata hai.

---

## **Table of Contents**  

1. [Introduction to "keyword" Data Type](#1)  
2. ["keyword" vs "text" Data Type](#2)  
3. [How "keyword" Data Type Works](#3)  
   - [Standard Analyzer vs Keyword Analyzer](#3-1)  
   - [Inverted Index Structure](#3-2)  
4. [Use Cases of "keyword" Data Type](#4)  
   - [Email Addresses](#4-1)  
   - [Order Status](#4-2)  
   - [Tags and Categories](#4-3)  
5. [Customizing the "keyword" Analyzer](#5)  
6. [Conclusion & Key Takeaways](#6)  

---

## **1. Introduction to "keyword" Data Type**  <a id="1"></a>

Elasticsearch me `keyword` data type ka use **filtering, sorting, aur aggregations** ke liye hota hai. Yeh **structured data** ke liye best hai jisme exact matching zaroori hoti hai.  

### **Keyword Data Type Use Cases:**  
- **Email Addresses:** `"user@example.com"`  
- **Order Status:** `"pending"`, `"shipped"`, `"delivered"`  
- **Tags:** `"electronics"`, `"books"`, `"clothing"`  

Lekin, iska **full-text search** ke liye use nahi hota. Uske liye `text` data type hota hai.  

---

## **2. "keyword" vs "text" Data Type**  <a id="2"></a>

| Feature         | `keyword` | `text` |
|---------------|-----------|--------|
| Tokenized?    | ❌ No | ✅ Yes |
| Full-Text Search? | ❌ No | ✅ Yes |
| Sorting Allowed? | ✅ Yes | ❌ No |
| Filtering Allowed? | ✅ Yes | ❌ No |
| Aggregations? | ✅ Yes | ❌ No |

### **Example Mapping:**  
```json
{
  "mappings": {
    "properties": {
      "title": { "type": "text" },
      "category": { "type": "keyword" }
    }
  }
}
```
- `title` full-text search ke liye tokenize hoga.  
- `category` exact match ke liye use hoga.  

---

## **3. How "keyword" Data Type Works**  <a id="3"></a>

### **3.1 Standard Analyzer vs Keyword Analyzer**  <a id="3-1"></a>

Elasticsearch me har field ek **analyzer** ke through process hoti hai.  
- `text` fields ke liye **standard analyzer** use hota hai jo text ko **tokenize** karta hai.  
- `keyword` fields ke liye **keyword analyzer** use hota hai jo **text ko as-it-is store** karta hai.  

#### **Keyword Analyzer Working**  
- **Koi modification nahi hoti.**  
- **Koi tokenization nahi hoti.**  
- **Text jaisa hai waisa hi store hota hai.**  

##### **Example: Analyze API**  
```json
POST _analyze
{
  "analyzer": "keyword",
  "text": "Hello World!"
}
```
**Output:**  
```json
{
  "tokens": [
    { "token": "Hello World!" }
  ]
}
```
- Poora string ek **single token** ke roop me store hota hai.  
- Koi space ya special character remove nahi hota.  

---

### **3.2 Inverted Index Structure**  <a id="3-2"></a>

Inverted index me `keyword` ka **poora text ek term** ke roop me store hota hai.  
Agar hum do email addresses store karein:  

```json
[
  { "email": "user@domain.com" },
  { "email": "admin@domain.com" }
]
```
To inverted index me ye kuch aisa dikhega:  

| Term | Document IDs |
|------|-------------|
| user@domain.com | 1 |
| admin@domain.com | 2 |

- Har email **poora string ke roop me ek single token** hai.  
- Elasticsearch sirf **exact matches** kar sakta hai.  

---

## **4. Use Cases of "keyword" Data Type**  <a id="4"></a>

### **4.1 Email Addresses**  <a id="4-1"></a>

Emails hamesha exact match ke liye store hote hain.  
Example mapping:  
```json
{
  "email": { "type": "keyword" }
}
```
Search Query:  
```json
{
  "query": {
    "term": { "email": "user@domain.com" }
  }
}
```
- Yahan **full match** hona zaroori hai.  
- Partial match ya case-insensitive match nahi hoga.  

---

### **4.2 Order Status**  <a id="4-2"></a>

Order status (`pending`, `shipped`, `delivered`) exact match ke liye ideal hai.  
```json
{
  "status": { "type": "keyword" }
}
```
Aggregation Example:  
```json
{
  "aggs": {
    "order_status_count": {
      "terms": { "field": "status" }
    }
  }
}
```
- Ye query har status ka count nikal degi.  

---

### **4.3 Tags and Categories**  <a id="4-3"></a>

Agar ek product ke tags hain:  
```json
{ "tags": ["electronics", "gadgets"] }
```
To `keyword` type hone par exact match kar sakte hain.  

```json
{
  "query": {
    "term": { "tags": "electronics" }
  }
}
```
- **Partial match nahi hoga** (`electro` search karne par `electronics` nahi milega).  

---

## **5. Customizing the "keyword" Analyzer**  <a id="5"></a>

Agar hume **case-insensitive match** chahiye to hum **lowercase filter** use kar sakte hain.  

```json
{
  "settings": {
    "analysis": {
      "analyzer": {
        "lowercase_keyword": {
          "type": "custom",
          "tokenizer": "keyword",
          "filter": ["lowercase"]
        }
      }
    }
  }
}
```
- Is analyzer me **sabhi letters lowercase** me convert ho jayenge.  

---

## **6. Conclusion & Key Takeaways**  <a id="6"></a>

✅ `keyword` data type structured data ke liye best hai.  
✅ **Sorting, filtering, aur aggregations** ke liye use hota hai.  
✅ **Full-text search ke liye use nahi hota.**  
✅ **Case-sensitive hota hai**, lekin analyzer customize kiya ja sakta hai.  

---