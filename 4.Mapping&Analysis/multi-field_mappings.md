# 📖 **Table of Contents**
1️⃣ [📌 Introduction: Multi-Field Mapping Kya Hai?](#1)  
2️⃣ [🔍 Elasticsearch Field Types Recap](#2)  
3️⃣ [🛠️ Multi-Field Mapping Kaise Define Karte Hain?](#3)  
4️⃣ [📊 Multi-Field Mapping Ka Use Case](#4)  
5️⃣ [🔎 Querying Multi-Fields](#5)  
6️⃣ [⚡ Advanced Use Cases](#6)  
7️⃣ [📝 Summary](#7)  

---

## 1️⃣ 📌 **Introduction: Multi-Field Mapping Kya Hai?** <a id="1"></a>
Jab hum Elasticsearch me ek field ko define karte hain, to hum uske liye ek specific data type set karte hain, jaise `text`, `keyword`, `integer`, etc.  

**Lekin kabhi-kabhi hume ek hi field ko different types se query karna padta hai.**  
Jaise ki ek `text` field pe full-text search bhi karna ho aur saath hi exact matching ke liye `keyword` field bhi chahiye ho.  

Aise situations me **multi-field mappings** ka use hota hai, jisme ek field ke multiple versions hote hain, jine alag-alag tarike se query kiya ja sakta hai.

---

## 2️⃣ 🔍 **Elasticsearch Field Types Recap** <a id="2"></a>
Multi-field mappings samajhne ke liye pehle `text` aur `keyword` fields ke difference ko samajhna zaroori hai:

| Field Type | Use Case | Features |
|------------|---------|----------|
| **text** | Full-text search (user input, blog content, etc.) | - Analyzed hota hai (lowercased, tokenized, stemming) |
| **keyword** | Exact matching, aggregations, sorting | - Analyzed nahi hota, original form me store hota hai |

**Example:**  
Agar ek `ingredients` field hai jo kisi recipe ka ingredient store karti hai, to:

- `text` type allow karega **full-text search** jaise `"spaghetti"` ke liye search karna.
- `keyword` type allow karega **exact match & aggregation** jaise **kitni recipes me `"Spaghetti"` use hua hai** ye count karna.

---

## 3️⃣ 🛠️ **Multi-Field Mapping Kaise Define Karte Hain?** <a id="3"></a>
Multi-field mappings define karne ke liye, hum `fields` parameter ka use karte hain:

### ✅ **Example: Mapping Creation**
```json
PUT recipes
{
  "mappings": {
    "properties": {
      "ingredients": {
        "type": "text",
        "fields": {
          "keyword": { "type": "keyword" }
        }
      }
    }
  }
}
```
### **Kya Ho Raha Hai?**
- `ingredients` field ka **default type `text` hai**, jo full-text search ke liye use hoga.
- Usme ek extra mapping **`keyword` name se add kiya**, jo exact matches aur aggregations ke liye use hoga.

---

## 4️⃣ 📊 **Multi-Field Mapping Ka Use Case** <a id="4"></a>
Socho ek food blog hai jisme recipes hain.  
Hume users ko **ingredients ke basis pe search aur aggregation dono features** dene hain.

**Example:**  
- **Search Functionality** -> "Spaghetti" search karna
- **Aggregation** -> Count karna ki **kitni recipes me Spaghetti use hota hai?**

Agar hum sirf `text` field use karein to aggregation nahi ho payegi.  
Agar sirf `keyword` field use karein to full-text search nahi ho payega.  
**Solution?** Multi-field mapping!

---

## 5️⃣ 🔎 **Querying Multi-Fields** <a id="5"></a>
Multi-field mapping ke baad hum **different ways me query** kar sakte hain:

### ✅ **1. Full-Text Search (`text` field)**
```json
GET recipes/_search
{
  "query": {
    "match": { "ingredients": "spaghetti" }
  }
}
```
**Kya Hoga?**  
Elasticsearch **analyzed form** me `spaghetti` ko search karega.

---

### ✅ **2. Exact Match Search (`keyword` field)**
```json
GET recipes/_search
{
  "query": {
    "term": { "ingredients.keyword": "Spaghetti" }
  }
}
```
**Kya Hoga?**  
Ye query **exact match** karegi. `"spaghetti"` aur `"Spaghetti"` alag treat honge.

---

### ✅ **3. Aggregation on `keyword` Field**
```json
GET recipes/_search
{
  "size": 0,
  "aggs": {
    "popular_ingredients": {
      "terms": { "field": "ingredients.keyword" }
    }
  }
}
```
**Kya Hoga?**  
- Ye aggregation **sabse jyada used ingredients ka count dikhayegi**.
- `text` field aggregation support nahi karta, isliye `keyword` use karna zaroori hai.

---

## 6️⃣ ⚡ **Advanced Use Cases** <a id="6"></a>

Multi-field mappings sirf `text` aur `keyword` tak limited nahi hain.  

Agar hume ek field ke **different language analyzers** chahiye, to bhi use kar sakte hain.  

### ✅ **Example: Different Analyzers**
```json
PUT products
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "fields": {
          "english": { "type": "text", "analyzer": "english" },
          "french": { "type": "text", "analyzer": "french" }
        }
      }
    }
  }
}
```
**Isme kya ho raha hai?**  
- `title` field ka ek **default mapping hai** jo normal `text` field hai.
- `title.english` me **English analyzer** apply hoga.
- `title.french` me **French analyzer** apply hoga.
- Hum **multi-language search bhi efficiently handle kar sakte hain.**

---

## 7️⃣ 📝 **Summary** <a id="7"></a>

| Feature | Description |
|---------|------------|
| **Multi-Field Mapping** | Ek field ko multiple types se define karne ka tarika |
| **Use Case** | Jab ek field ko full-text search aur exact match dono ke liye use karna ho |
| **`text` Field** | Full-text search ke liye |
| **`keyword` Field** | Exact match, aggregation aur sorting ke liye |
| **Querying** | `match` (text ke liye), `term` (keyword ke liye), `aggregations` (keyword ke liye) |
| **Advanced Use Cases** | Different analyzers, multi-language search, stemming |

---

## 🎯 **Final Thoughts**
- **Multi-field mapping** bahut **powerful feature** hai jo ek hi field ko **multiple ways me query karne ki flexibility** deta hai.
- `text` aur `keyword` ka combination **sabse common use case** hai.
- Aggregations, sorting, and exact searches ke liye `keyword` use karna zaroori hai.

