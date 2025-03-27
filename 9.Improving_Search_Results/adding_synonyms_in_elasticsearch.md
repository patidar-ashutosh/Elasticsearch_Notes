## 📝 **Adding Synonyms in Elasticsearch** 🔍

## 📌 **Table of Contents**  
1️⃣ [Introduction to Synonyms](#1)  
2️⃣ [Custom Analyzer with Synonym Token Filter](#2)  
3️⃣ [Syntax for Defining Synonyms](#3)  
4️⃣ [How Elasticsearch Handles Synonyms](#4)  
5️⃣ [Different Types of Synonym Rules](#5)  
6️⃣ [Testing Synonyms with the Analyze API](#6)  
7️⃣ [Searching with Synonyms](#7)  
8️⃣ [Use Cases for Synonyms in Search](#8)  
9️⃣ [Conclusion](#9)

---

## 🔥 **1. Introduction to Synonyms** <a id="1"></a>

Elasticsearch me synonyms ka use **better search results** ke liye kiya jata hai. Synonyms allow karte hain ki **ek word ka equivalent word bhi search me match ho sake**. 

Jaise:  
- **"awful"** ko **"terrible"** se replace karna  
- **"awesome"** ko **"great" aur "super"** se replace karna  
- **"Elasticsearch, Logstash, Kibana"** ko ek common term **"ELK"** se replace karna  

Ye **search experience ko improve** karta hai taaki users ko relevant results mil sake. 

---

## ⚙️ **2. Custom Analyzer with Synonym Token Filter** <a id="2"></a>

Elasticsearch me synonyms add karne ke liye **custom analyzer** use hota hai, jo ek **custom token filter** ke through synonyms ko define karta hai.  

```json
PUT synonyms
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer": {
          "tokenizer": "standard",
          "filter": ["lowercase", "my_synonym_filter"]
        }
      },
      "filter": {
        "my_synonym_filter": {
          "type": "synonym",
          "synonyms": [
            "awful => terrible",
            "awesome => great, super",
            "elasticsearch, logstash, kibana => elk",
            "weird, strange"
          ]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "description": {
        "type": "text",
        "analyzer": "my_analyzer"
      }
    }
  }
}
```

### 🛠 **Explanation**
- `"my_analyzer"`: Custom analyzer jo tokenizer aur filters define karta hai  
- `"lowercase"`: Saare words ko lowercase me convert karega  
- `"my_synonym_filter"`: Custom synonym filter jo words replace karega  

---

## 🖋 **3. Syntax for Defining Synonyms** <a id="3"></a>

Synonyms define karne ke multiple tarike hote hain:

| Syntax | Explanation | Example |
|--------|------------|---------|
| `A => B` | Replace `A` with `B` | `"awful => terrible"` |
| `A => B, C` | Replace `A` with multiple terms `B` and `C` | `"awesome => great, super"` |
| `A, B => C` | Replace `A` or `B` with `C` | `"elasticsearch, logstash, kibana => elk"` |
| `A, B` | No replacement, just store both words at the same position | `"weird, strange"` |

---

## 🔄 **4. How Elasticsearch Handles Synonyms** <a id="4"></a>

Synonyms **inverted index** me store hote hain, jo searching ke process ko optimize karta hai.

🔹 **Example:**  
Agar **"awesome"** ko **"great, super"** se replace kiya gaya hai, to jab koi **"great"** ya **"super"** search karega to **"awesome"** wala document bhi match hoga.

📌 **Important:**  
- **Match queries** me synonyms work karte hain kyunki ye bhi analyzer use karte hain  
- **Term queries** me synonyms work nahi karte kyunki wo analyze nahi hote  

---

## 🎭 **5. Different Types of Synonym Rules** <a id="5"></a>

Elasticsearch me synonyms define karne ke multiple approaches hain:

1️⃣ **Simple Replacement**  
   ```json
   "awful => terrible"
   ```
   📌 `"awful"` replace hoke `"terrible"` ho jayega.

2️⃣ **Multiple Replacements**  
   ```json
   "awesome => great, super"
   ```
   📌 `"awesome"` replace hoke `"great"` aur `"super"` dono ho sakta hai.

3️⃣ **Group Replacement**  
   ```json
   "elasticsearch, logstash, kibana => elk"
   ```
   📌 `"elasticsearch"`, `"logstash"` aur `"kibana"` teeno ko replace karke `"elk"` se match karna.

4️⃣ **Position-Based Matching**  
   ```json
   "weird, strange"
   ```
   📌 Isme koi replacement nahi hota, sirf synonyms same position par store hote hain.

---

## 🧪 **6. Testing Synonyms with the Analyze API** <a id="6"></a>

Synonyms ka test karne ke liye **Analyze API** ka use hota hai.

```json
POST synonyms/_analyze
{
  "analyzer": "my_analyzer",
  "text": "awesome"
}
```

🔹 **Expected Output**
```json
{
  "tokens": [
    { "token": "great", "position": 0 },
    { "token": "super", "position": 0 }
  ]
}
```
📌 `"awesome"` ko `"great"` aur `"super"` me replace kar diya gaya.

---

## 🔎 **7. Searching with Synonyms** <a id="7"></a>

Synonyms search queries me kaise kaam karte hain, ye samajhne ke liye do queries run karte hain.

### ✅ **Search for "great"**
```json
GET synonyms/_search
{
  "query": {
    "match": {
      "description": "great"
    }
  }
}
```
📌 **Output:** Ye query `"awesome"` wale documents ko match karegi kyunki `"awesome"` `"great"` se replace ho chuka hai.

### ✅ **Search for "awesome"**
```json
GET synonyms/_search
{
  "query": {
    "match": {
      "description": "awesome"
    }
  }
}
```
📌 **Output:** Ye bhi `"great"` aur `"super"` wale documents ko match karegi kyunki search query bhi same analysis process follow karegi.

---

## 🎯 **8. Use Cases for Synonyms in Search** <a id="8"></a>

✅ **E-commerce Search:**  
   - `"mobile"` ko `"smartphone"` se replace karna  
   - `"TV"` ko `"television"` aur `"smart TV"` se replace karna  

✅ **Real Estate Search:**  
   - `"flat"` aur `"apartment"` ko same treat karna  

✅ **Healthcare Search:**  
   - `"heart attack"` aur `"cardiac arrest"` ko ek synonym banana  

✅ **Job Portals:**  
   - `"developer"` aur `"software engineer"` ko synonyms banana  

---

## 🎉 **9. Conclusion**  <a id="9"></a>

Synonyms **search accuracy** badhane ke liye ek powerful feature hai. Isme:  
- **Custom Analyzer** create karna  
- **Synonym Token Filter** ka use karna  
- **Different types of synonym rules define karna**  
- **Analyze API se synonyms test karna**  

Agar synonyms ka sahi use kiya jaye to search experience bahut **better aur efficient** ho sakta hai. 🚀