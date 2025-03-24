# **Mapping Recommendations in Elasticsearch**

## **Table of Contents**  
1️⃣ [Introduction](#1)  
2️⃣ [Explicit Mapping vs Dynamic Mapping](#2)  
   - 🔹 [Dynamic Mapping Issues](#2-1)  
   - 🔹 [Why Use Explicit Mapping?](#2-2)  

3️⃣ [Setting "dynamic" Parameter](#3)  
4️⃣ [Avoid Mapping Fields as Both "text" and "keyword"](#4)  
5️⃣ [Disabling Type Coercion](#5)  
6️⃣ [Choosing the Right Numeric Data Type](#6)  
7️⃣ [Optimizing Storage with Mapping Parameters](#7)  
   - 🔹 [Disabling "doc_values"](#7-1)  
   - 🔹 [Disabling "norms"](#7-2)  
   - 🔹 [Disabling "index"](#7-3)  
   
8️⃣ [When to Optimize Mapping](#8)  
9️⃣ [Conclusion](#9)  

---

## **1. Introduction** <a id="1"></a>
Elasticsearch me **mapping** kaafi important hota hai kyunki ye batata hai ki har field ka data type kya hoga aur us field pe kaunsi optimizations apply hogi. Agar mapping sahi set nahi ki gayi, to **storage ka zyada use hoga, query performance slow ho sakti hai, aur unnecessary fields store ho sakti hain.**

Is guide me **best practices aur recommendations** diye gaye hain jo Elasticsearch mapping ko optimize karne me madad karenge.

---

## **2. Explicit Mapping vs Dynamic Mapping** <a id="2"></a>

### **Dynamic Mapping Issues** <a id="2-1"></a>
**Dynamic mapping** Elasticsearch ka ek feature hai jo naye fields ko automatically detect kar leta hai aur unka data type set kar deta hai. Yeh development phase me convenient hota hai, **lekin production me problem create kar sakta hai:**
✅ **Incorrect data types**: Elasticsearch galat type assign kar sakta hai.
✅ **Unoptimized storage**: Zyada space consume ho sakti hai.
✅ **Unnecessary keyword fields**: Default dynamic mapping text fields ko **both text and keyword** bana deta hai, jo extra disk space leta hai.

### **Why Use Explicit Mapping?** <a id="2-2"></a>
Agar tumhare paas **bahut saare documents** store karne hain, to **explicit mapping** use karna better hai. Iska matlab hai ki tum **pehle se hi har field ka type define kar do** taaki optimization achhe se ho sake.

---

## **3. Setting "dynamic" Parameter** <a id="3"></a>
"dynamic" parameter ko **"strict"** set karne ka recommendation diya gaya hai.

```json
{
  "mappings": {
    "dynamic": "strict",
    "properties": {
      "name": { "type": "text" },
      "age": { "type": "integer" }
    }
  }
}
```

**Explanation:**
✅ **"strict"** karne se sirf wahi fields index honge jo pehle se mapping me defined hain.
✅ Agar koi unknown field aaye, to error throw hogi, jo unexpected behavior prevent karega.
✅ **"false"** set karne se unknown fields ko ignore kiya jayega, par query karne par match nahi hoga.

---

## **4. Avoid Mapping Fields as Both "text" and "keyword"** <a id="4"></a>
Elasticsearch me dynamic mapping by default har string field ko **"text" + "keyword"** dono bana deta hai. Agar yeh zaroori nahi hai, to avoid karo.

### **Kab "text" aur "keyword" use karein?**
| Field Usage | Mapping Type |
|------------|--------------|
| Full-text search | `text` |
| Aggregation, sorting, filtering | `keyword` |
| Dono cheezein chahiye | `text` + `keyword` |

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

---

## **5. Disabling Type Coercion** <a id="5"></a>
Elasticsearch **galat type ke data** ko automatically correct kar sakta hai (coercion), jo problem create kar sakta hai.

```json
{
  "mappings": {
    "coerce": false,
    "properties": {
      "price": { "type": "integer" }
    }
  }
}
```

**Issue:** "100.5" ko **integer** me convert karne ki koshish karega, jo galat ho sakta hai.
✅ **Best Practice**: **Correct data type bhejo, ya coercion disable karo.**

---

## **6. Choosing the Right Numeric Data Type** <a id="6"></a>
Agar number ka size zyada nahi hai, to **chhota data type** use karo taaki disk space save ho.

| Data Type | Size | Use Case |
|-----------|------|----------|
| Integer | 4 bytes | Chhoti range wale numbers |
| Long | 8 bytes | Large numbers |
| Float | 4 bytes | Approximate decimal values |
| Double | 8 bytes | High precision decimal |

```json
{
  "mappings": {
    "properties": {
      "age": { "type": "integer" },
      "price": { "type": "float" }
    }
  }
}
```

---

## **7. Optimizing Storage with Mapping Parameters** <a id="7"></a>
### **Disabling "doc_values"** <a id="7-1"></a>
Agar field **sorting/aggregation** ke liye nahi chahiye, to "doc_values" **false** set karo.
```json
{
  "mappings": {
    "properties": {
      "description": { "type": "text", "doc_values": false }
    }
  }
}
```
✅ Space bachane ke liye **large text fields** me use hota hai.

### **Disabling "norms"** <a id="7-2"></a>
Agar **relevance scoring** chahiye nahi, to "norms" disable kar do.
```json
{
  "mappings": {
    "properties": {
      "category": { "type": "keyword", "norms": false }
    }
  }
}
```
✅ Aggregation aur filtering me useful hota hai.

### **Disabling "index"** <a id="7-3"></a>
Agar kisi field pe search nahi karni, to indexing disable kar do.
```json
{
  "mappings": {
    "properties": {
      "meta_info": { "type": "text", "index": false }
    }
  }
}
```
✅ Metadata fields ke liye useful hai.

---

## **8. When to Optimize Mapping** <a id="8"></a>
Agar tumhare paas **1 million+ documents** store hone wale hain, to **pehle se optimized mapping define karo.**
Agar aisa nahi karoge, to later **reindexing** karni padegi, jo costly ho sakta hai.

✅ **Small dataset?** - Default mapping chalega.
✅ **Large dataset?** - Optimize from the start.

---

## **9. Conclusion** <a id="9"></a>
✔ **Explicit mapping production me better hai.**
✔ **"Strict" dynamic mapping control provide karta hai.**
✔ **Unnecessary "text + keyword" mapping avoid karo.**
✔ **Numeric fields ka best type select karo.**
✔ **Storage optimize karne ke liye doc_values, norms, aur index disable karo.**

