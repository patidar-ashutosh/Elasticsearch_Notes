### **Dynamic Mapping in Elasticsearch**

## **Table of Contents**  

1️⃣ [Introduction to Dynamic Mapping](#1)  
2️⃣ [Disabling Dynamic Mapping](#2)  
3️⃣ [Understanding "_source" and Indexing](#3)  
4️⃣ [Explicit Mapping Requirement When Dynamic is False](#4)  
5️⃣ [Strict Mode in Dynamic Mapping](#5)  
6️⃣ [Inheritance in Dynamic Mapping](#6)  
7️⃣ [Overriding Inheritance for Specific Fields](#7)  
8️⃣ [Numeric Detection in Dynamic Mapping](#8)  
9️⃣ [Date Detection in Dynamic Mapping](#9)  
🔟 [Conclusion](#10)  

---

## **1. Introduction to Dynamic Mapping**  <a id="1"></a>
**Dynamic Mapping** ek feature hai Elasticsearch me jo automatically naye fields ka mapping create kar deta hai jab naye documents index hote hain. Yeh feature chhoti chhoti applications ke liye useful hota hai jisme pre-defined schema maintain karna zaroori nahi hota.  

**Default Behavior:**  
- Jab koi naya document insert hota hai aur uska koi field mapping me define nahi hota, to Elasticsearch us field ke data type ko detect karke automatically mapping create kar deta hai.  
- Example: Agar ek JSON document me `"age": 25` diya hai aur mapping me `"age"` define nahi hai, to Elasticsearch automatically `"age"` ka type `integer` set kar dega.  

---

## **2. Disabling Dynamic Mapping**  <a id="2"></a>
Agar tum chahte ho ki Elasticsearch naye fields ko automatically mapping me add na kare, to tum **dynamic mapping ko disable** kar sakte ho.  

Iske liye `"dynamic": false` setting use hoti hai:

```json
PUT my_index
{
  "mappings": {
    "dynamic": false,
    "properties": {
      "first_name": { "type": "text" }
    }
  }
}
```
**Effect:**  
- Agar tum koi document index karoge jo `"first_name"` ke alawa kisi aur field ko contain karega, to wo document index to ho jayega lekin naye field ki mapping create nahi hogi.  
- Example:  
  ```json
  POST my_index/_doc/1
  {
    "first_name": "Rahul",
    "last_name": "Sharma"
  }
  ```
  - Document store ho jayega.  
  - **"last_name" field query ke liye available nahi hoga**, kyunki wo indexed nahi hua.  

---

## **3. Understanding "_source" and Indexing**  <a id="3"></a>
Jab `"dynamic": false` hota hai, to naye fields **_source object** me store ho jate hain lekin unka mapping index me nahi hota.  

- `_source` object Elasticsearch me **original document** ko store karta hai.  
- Query karne ke liye fields ka indexed hona zaroori hota hai.  

Agar `"last_name"` query karne ki koshish karoge:
```json
GET my_index/_search
{
  "query": {
    "match": { "last_name": "Sharma" }
  }
}
```
- **Koi result nahi milega**, kyunki `last_name` indexed nahi hua.  

---

## **4. Explicit Mapping Requirement When Dynamic is False**  <a id="4"></a>
Jab `"dynamic": false` set hota hai, to naye fields **automatically indexed nahi hote**, unhe manually define karna padta hai.  

Example:  
```json
PUT my_index/_mapping
{
  "properties": {
    "last_name": { "type": "text" }
  }
}
```
Ab `last_name` field query me use ho sakta hai.  

---

## **5. Strict Mode in Dynamic Mapping**  <a id="5"></a>
Elasticsearch me `"dynamic"` ke liye ek aur value hoti hai: `"strict"`  
- Jab `"dynamic": "strict"` hota hai, to agar document me koi unmapped field hoti hai, to Elasticsearch **error throw** kar deta hai.  

Example:  
```json
PUT my_index
{
  "mappings": {
    "dynamic": "strict",
    "properties": {
      "first_name": { "type": "text" }
    }
  }
}
```
Agar tum aisa document index karne ki koshish karoge:
```json
POST my_index/_doc/1
{
  "first_name": "Rahul",
  "last_name": "Sharma"
}
```
To error aayega:
```json
{
  "error": {
    "reason": "mapping set to strict, dynamic introduction of [last_name] within [my_index] is not allowed"
  }
}
```

---

## **6. Inheritance in Dynamic Mapping**  <a id="6"></a>
Dynamic mapping settings **nested fields** par bhi apply hoti hai.  

Example:  
```json
PUT computers
{
  "mappings": {
    "dynamic": "strict",
    "properties": {
      "name": { "type": "text" },
      "specifications": {
        "properties": {
          "cpu": { 
            "properties": { "name": { "type": "text" } }
          }
        }
      }
    }
  }
}
```
- `"specifications"` field `"dynamic": "strict"` inherit karega.  
- Agar tum `"cpu"` ke andar `"frequency"` field add karne ki koshish karoge, to **error aayega**.  

---

## **7. Overriding Inheritance for Specific Fields**  <a id="7"></a>
Agar tum kisi **specific field ke liye dynamic mapping enable** karna chahte ho, to us field ka `"dynamic": true"` set kar sakte ho.  

Example:
```json
PUT computers
{
  "mappings": {
    "dynamic": "strict",
    "properties": {
      "name": { "type": "text" },
      "specifications": {
        "dynamic": true,
        "properties": {
          "cpu": {
            "properties": { "name": { "type": "text" } }
          }
        }
      }
    }
  }
}
```
- `"specifications"` field ke andar naye fields **automatically detect** honge.  

---

## **8. Numeric Detection in Dynamic Mapping**  <a id="8"></a>
**Numeric detection** enable karne ke liye `"numeric_detection": true"` set karna hota hai.  

```json
PUT my_index
{
  "mappings": {
    "numeric_detection": true
  }
}
```
Iska effect:  
- Agar ek field `"25"` string me bhi aaye aur `"25"` number me bhi aaye, to Elasticsearch automatically decide karega ki wo `"float"` ya `"long"` hoga.  

Example:
```json
POST my_index/_doc/1
{
  "price": "99.99"
}
```
- `"price"` ka type `float` set ho jayega.  

---

## **9. Date Detection in Dynamic Mapping**  <a id="9"></a>
Default me Elasticsearch string values ko **date formats** ke liye check karta hai.  

Agar tum **date detection disable** karna chahte ho:
```json
PUT my_index
{
  "mappings": {
    "date_detection": false
  }
}
```
Agar tum **custom date formats allow** karna chahte ho:
```json
PUT my_index
{
  "mappings": {
    "dynamic_date_formats": ["yyyy/MM/dd", "dd-MM-yyyy"]
  }
}
```
Iska effect:  
- `"2024/03/04"` ya `"04-03-2024"` automatically `date` type me convert ho jayenge.  

---

## **10. Conclusion**  <a id="10"></a>
- **Dynamic Mapping** Elasticsearch me naye fields ka mapping automatically create karne ka ek powerful feature hai.  
- `"dynamic": false"` naye fields ko **ignore** karta hai, but store karta hai.  
- `"dynamic": "strict"` naye fields ko **reject** karta hai.  
- **Inheritance** dynamic settings ko **nested objects** tak propagate karti hai.  
- **Numeric & Date Detection** Elasticsearch ko **intelligent type conversion** me help karti hai.  

Agar tumhe **full control** chahiye to manual mapping define karna best practice hai! 🚀