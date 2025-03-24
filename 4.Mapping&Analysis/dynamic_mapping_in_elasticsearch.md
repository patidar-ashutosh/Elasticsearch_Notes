# **Dynamic Mapping in Elasticsearch**

## **Table of Contents**  

1️⃣ [Introduction to Dynamic Mapping](#1)  
2️⃣ [How Dynamic Mapping Works](#2)  
3️⃣ [Examples of Dynamic Mapping](#3)  
4️⃣ [Data Type Detection](#4)  
   - [Date Detection](#4-1)  
   - [Numeric Detection](#4-2)  

5️⃣ [Handling Strings: Text vs. Keyword](#5)  
6️⃣ [Handling Objects and Arrays](#6)  
7️⃣ [Optimizing Dynamic Mapping](#7)  
8️⃣ [Disabling or Controlling Dynamic Mapping](#8)  
9️⃣ [Conclusion](#9)  

---

## **1. Introduction to Dynamic Mapping** <a id="1"></a>

Elasticsearch me documents ko index karne ke liye humein pehle se field mappings define karni padti hai. Lekin, agar hum manually har ek field ka mapping na karna chahein, toh Elasticsearch **Dynamic Mapping** feature ka use karta hai.

### **Dynamic Mapping Kya Hai?**

- **Elasticsearch automatically fields ka mapping create kar leta hai** jab kisi naye document me naye fields aayein.
- Iska matlab hai ki tumhe pehle se mapping define karne ki zaroorat nahi hoti.
- Yeh feature Elasticsearch ko **flexible aur easy-to-use** banata hai.

---

## **2. How Dynamic Mapping Works** <a id="2"></a>

Jab bhi Elasticsearch kisi naye document me naye fields dekhta hai, toh:

1. **Agar index exist nahi karta**, toh ek naya index create hota hai.
2. **Agar mapping pehle se defined nahi hai**, toh Elasticsearch automatically field mapping create karta hai.
3. **Elasticsearch field ke data type ko detect karta hai** aur uske according mapping assign karta hai.

---

## **3. Examples of Dynamic Mapping** <a id="3"></a>

Agar hum ek naye document ko index karein jisme pehle se koi mapping define nahi hai:

```json
PUT my_index/_doc/1
{
  "name": "Elasticsearch",
  "created_at": "2023-03-01",
  "in_stock": 50,
  "tags": ["search", "database"]
}
```

Elasticsearch automatically in fields ke liye mapping create karega:

```json
{
  "name": { "type": "text" },
  "created_at": { "type": "date" },
  "in_stock": { "type": "long" },
  "tags": { "type": "text", "fields": { "keyword": { "type": "keyword" } } }
}
```

---

## **4. Data Type Detection** <a id="4"></a>

### **4.1 Date Detection** <a id="4-1"></a>

- JSON me **date ka specific data type nahi hota**.
- Elasticsearch **date detection** ka use karke automatically identify karta hai ki yeh ek date value hai.

Example:

```json
"created_at": "2023-03-01"
```

Yeh **date** ke roop me identify hogi.

### **4.2 Numeric Detection** <a id="4-2"></a>

- Agar ek **string ke andar sirf numbers hain**, toh Elasticsearch usko **integer ya float** ke roop me map kar sakta hai.
- Numeric detection **by default disabled** hoti hai.

---

## **5. Handling Strings: Text vs. Keyword** <a id="5"></a>

- **Text:** Full-text search ke liye use hota hai.
- **Keyword:** Exact match, sorting aur aggregations ke liye use hota hai.

Example:

```json
"tags": { "type": "text", "fields": { "keyword": { "type": "keyword" } } }
```

- Agar **full-text search** karni ho, toh **text** mapping use hoti hai.
- Agar **exact filtering ya sorting** karni ho, toh **keyword** mapping use hoti hai.

---

## **6. Handling Objects and Arrays** <a id="6"></a>

- **Objects:** Elasticsearch me objects ko **nested fields** ke roop me treat kiya jata hai.
- **Arrays:** Elasticsearch arrays ko **multiple values** jaise treat karta hai.

Example:

```json
"cpu": {
  "cores": 8,
  "speed": "3.6GHz"
}
```

Isko Elasticsearch **nested structure** me map karega.

---

## **7. Optimizing Dynamic Mapping** <a id="7"></a>

- **Unnecessary text/keyword mapping avoid karo** agar uski zaroorat nahi hai.
- **Manually mapping define karo** agar tumhe pata hai ki kis type ka data aayega.
- **Ignore fields jo use nahi honi chahiye**.

Example:

```json
"ignore_above": 256
```

Iska matlab hai agar **256 characters se bada data aaya**, toh Elasticsearch usko ignore karega.

---

## **8. Disabling or Controlling Dynamic Mapping** <a id="8"></a>

Agar tum dynamic mapping ko **disable** ya **restrict** karna chaahte ho:

```json
PUT my_index
{
  "mappings": {
    "dynamic": "strict"
  }
}
```

- **strict** karne se sirf manually defined fields hi allow hongi.
- **false** karne se naye fields automatically add nahi honge.

---

## **9. Conclusion** <a id="9"></a>

- **Dynamic Mapping** Elasticsearch ko **flexible** aur **user-friendly** banata hai.
- **Automatically data types detect karne ki ability** Elasticsearch ko powerful banati hai.
- Lekin, **optimization aur manual mapping** ka use karke storage aur performance improve kiya ja sakta hai.

Agar tumhe **large-scale applications** banani hain, toh **manual mapping aur schema design** ka dhyan dena zaroori hai!