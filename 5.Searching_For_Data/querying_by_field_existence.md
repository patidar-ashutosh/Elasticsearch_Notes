### Querying by Field Existence in Elasticsearch  

## 📌 **Table of Contents**  

1️⃣ [**Introduction**](#1)  
2️⃣ [**Understanding the `exists` Query**](#2)  
3️⃣ [**How Empty Values Affect Indexing**](#3)  
4️⃣ [**Examples with Expected Output**](#4)  
5️⃣ [**Handling Non-Indexed Fields**](#5)  
6️⃣ [**Using `bool` Query to Invert `exists` Query**](#6)  
7️⃣ [**Conclusion**](#7)  

---  

## 1️⃣ Introduction  <a id="1"></a>

Elasticsearch me kai baar hume yeh check karna hota hai ki kisi document me ek particular field exist karti hai ya nahi. Iske liye `exists` query ka use kiya jata hai, jo SQL ke `IS NOT NULL` condition jaisa kaam karti hai.  

## 2️⃣ Understanding the `exists` Query  <a id="2"></a>

Agar kisi field ka indexed value exist karta hai, to `exists` query us field ko match karegi. Agar field ka value empty ho (jaise `NULL` ya empty array), to yeh query us document ko match nahi karegi.  

💡 **Example Query:**  
```json
GET my_index/_search
{
  "query": {
    "exists": {
      "field": "tags.keyword"
    }
  }
}
```
🔍 **Explanation:**  
- Yeh query sirf un documents ko match karegi jisme `tags.keyword` field ka indexed value exist karega.  

## 3️⃣ How Empty Values Affect Indexing  <a id="3"></a>

Agar hum kisi field ka value `NULL` ya empty array dete hain, to Elasticsearch us value ko index nahi karta. Iska matlab yeh hua ki `exists` query in documents ko match nahi karegi.  

💡 **Example Document Indexing:**  
```json
PUT my_index/_doc/1
{
  "name": "Document 1",
  "tags": ["elasticsearch", "query"]
}

PUT my_index/_doc/2
{
  "name": "Document 2",
  "tags": []
}
```
👉 **Explanation:**  
- Document `1` ka `tags` field ek non-empty array hai, to yeh index ho jayega.  
- Document `2` ka `tags` field empty array hai, to Elasticsearch isko index nahi karega.  

## 4️⃣ Examples with Expected Output  <a id="4"></a>

💡 **Running the `exists` Query:**  
```json
GET my_index/_search
{
  "query": {
    "exists": {
      "field": "tags"
    }
  }
}
```
📌 **Expected Output:**  
```json
{
  "hits": {
    "total": 1,
    "hits": [
      {
        "_source": {
          "name": "Document 1",
          "tags": ["elasticsearch", "query"]
        }
      }
    ]
  }
}
```
✔️ **Explanation:**  
- Sirf `Document 1` match karega kyunki `Document 2` ka `tags` field empty tha aur index nahi hua.  

## 5️⃣ Handling Non-Indexed Fields  <a id="5"></a>

Kuch aur reasons jinki wajah se field index nahi ho sakti:  
✅ **Field missing from indexing request** (Agar field diya hi nahi)  
✅ **`index: false` mapping** (Agar explicitly indexing disable ki gayi ho)  
✅ **`ignore_above` parameter exceed ho jaye** (Agar keyword field ki max length cross ho jaye)  
✅ **Wrong data type indexing (`ignore_malformed: true`)**  

## 6️⃣ Using `bool` Query to Invert `exists` Query  <a id="6"></a>

Agar hume un documents ko fetch karna ho jisme koi field indexed nahi hui ho, to hum `bool` query ka use kar sakte hain.  

💡 **Example Query:**  
```json
GET my_index/_search
{
  "query": {
    "bool": {
      "must_not": {
        "exists": {
          "field": "tags"
        }
      }
    }
  }
}
```
📌 **Expected Output:**  
```json
{
  "hits": {
    "total": 1,
    "hits": [
      {
        "_source": {
          "name": "Document 2",
          "tags": []
        }
      }
    ]
  }
}
```
✔️ **Explanation:**  
- Is query me humne `must_not` condition use ki hai taaki sirf wahi documents match ho jo `tags` field ka indexed value contain nahi karte.  

## 7️⃣ Conclusion  <a id="7"></a>
- `exists` query ka use `IS NOT NULL` jaisa hota hai.  
- Agar field empty array ya `NULL` ho to yeh index nahi hoti.  
- `bool` query ka use karke hum `exists` query ko invert kar sakte hain.  

