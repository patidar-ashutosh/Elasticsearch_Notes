### **Overview of Data Types in Elasticsearch**  

Data types documents ke fields ko define karne aur efficiently search operations perform karne ke liye use hote hain.  

**We can use data types during the mapping of index**

---

## **Table of Contents**  
1. [Introduction to Data Types](#1)  
2. [Basic Data Types](#2)  
   - [String Types: `text` & `keyword`](#2-1)  
   - [Numeric Types](#2-2)  
   - [Boolean Type](#2-3)  
   - [Date Type](#2-4)  
3. [Specialized Data Types](#3)  
   - [IP Data Type](#3-1)  
   - [Geo Data Types](#3-2)  
   - [Completion Data Type](#3-3)  
4. [Complex Data Types](#4)  
   - [Object Data Type](#4-1)  
   - [Nested Data Type](#4-2)  
5. [How Elasticsearch Stores Objects Internally](#5)  
6. [Comparison: Object vs. Nested Data Type](#6)  
7. [Conclusion & Key Takeaways](#7)  
8. [Example of Data Types During Mapping](#8)  

---

## **1. Introduction to Data Types**  <a id="1"></a>

Elasticsearch me har field ka ek specific data type hota hai jo ye define karta hai ki us field me kis type ka data store hoga. Ye data types Elasticsearch ko efficiently indexing aur searching karne me help karte hain.  

Kuch basic data types programming languages jaise hi hote hain, jaise ki strings, numbers, boolean, aur dates. Lekin Elasticsearch kuch special aur complex data types bhi provide karta hai jo ki unique use cases ke liye design kiye gaye hain.  

---

## **2. Basic Data Types**  <a id="2"></a>

### **a -> String Types: `text` & `keyword`**  <a id="2-1"></a>
Elasticsearch me strings ko represent karne ke liye do alag-alag data types hote hain:  
1. **`text`** → Jab humein full-text search karni hoti hai  
2. **`keyword`** → Jab humein exact match chahiye  

| Feature            | `text` | `keyword` |
|-------------------|--------|-----------|
| Tokenized?       | Yes    | No        |
| Full-Text Search | Yes    | No        |
| Sorting Allowed? | No     | Yes       |
| Aggregations?    | No     | Yes       |

**Example:**  
```json
{
  "title": { "type": "text" },
  "category": { "type": "keyword" }
}
```
- `title` field full-text search ke liye tokenize hoga.  
- `category` field ka exact match hoga aur sorting/filtering ke liye use kiya ja sakta hai.  

---

### **b -> Numeric Types**  <a id="2-2"></a>
Elasticsearch numeric values ko store karne ke liye multiple data types provide karta hai:  
- **Integer Types:** `byte`, `short`, `integer`, `long`  
- **Floating-Point Types:** `float`, `double`, `half_float`, `scaled_float`  

**Example:**  
```json
{
  "price": { "type": "float" },
  "stock": { "type": "integer" }
}
```

---

### **c -> Boolean Type**  <a id="2-3"></a>
Ye field sirf `true` ya `false` values store karta hai.  
**Example:**  
```json
{
  "is_active": { "type": "boolean" }
}
```

---

### **d -> Date Type**  <a id="2-4"></a>
Date values Elasticsearch me timestamps ke format me store hote hain. Default format `yyyy-MM-dd'T'HH:mm:ssZ` hota hai.  
**Example:**  
```json
{
  "published_date": { "type": "date", "format": "yyyy-MM-dd" }
}
```

---

## **3. Specialized Data Types**  <a id="3"></a>

### **a -> IP Data Type**  <a id="3-1"></a>
IP addresses store karne ke liye `ip` data type use hota hai.  
**Example:**  
```json
{
  "user_ip": { "type": "ip" }
}
```

---

### **b -> Geo Data Types**  <a id="3-2"></a>
Agar geographical locations ko store aur search karna ho to ye data types use hote hain:  
- `geo_point`: Latitude & longitude points  
- `geo_shape`: Complex shapes like polygons  

**Example:**  
```json
{
  "location": { "type": "geo_point" }
}
```

---

### **c -> Completion Data Type**  <a id="3-3"></a>
Ye data type **auto-complete suggestions** ke liye use hota hai.  
**Example:**  
```json
{
  "suggest": { "type": "completion" }
}
```

---

## **4. Complex Data Types**  <a id="4"></a>

### **a -> Object Data Type**  <a id="4-1"></a>
Elasticsearch me documents JSON format me store hote hain, aur kabhi-kabhi ek field ke andar aur bhi JSON objects hote hain. Inhe `object` data type ke andar store kiya jata hai.  

**Example:**  
```json
{
  "user": {
    "type": "object",
    "properties": {
      "name": { "type": "text" },
      "age": { "type": "integer" }
    }
  }
}
```

---

### **b -> Nested Data Type**  <a id="4-2"></a>
Agar ek object ke andar **array of objects** ho, to Elasticsearch automatically objects ko flatten kar deta hai, jisse relationships **break ho sakti hain**. Is problem ko solve karne ke liye `nested` data type use hota hai.  

#### **Problem Without Nested Type:**  
```json
{
  "product": "Laptop",
  "reviews": [
    { "user": "John Doe", "rating": 3.5 },
    { "user": "Alice", "rating": 5.0 }
  ]
}
```
Yahan problem ye hai ki agar hum query karein:  
- `"John Doe"` ne 4 ya usse jyada rating di ho,  
To **incorrect results** aa sakte hain kyunki Elasticsearch data ko mix kar deta hai.

#### **Solution Using Nested Type:**  
```json
{
  "reviews": {
    "type": "nested",
    "properties": {
      "user": { "type": "text" },
      "rating": { "type": "float" }
    }
  }
}
```
Ab har `review` ek **separate document** ki tarah store hoga aur query correctly kaam karegi.  

---

## **5. How Elasticsearch Stores Objects Internally**  <a id="5"></a>
Elasticsearch internally Apache Lucene ka use karta hai, jo ki objects ko support nahi karta. Is wajah se:  
- Objects **flatten** ho jate hain aur **dot notation** ka use hota hai.  
- Nested objects **hidden documents** ki tarah store hote hain.  

**Example:**  
```json
{
  "user": {
    "name": "John",
    "address": {
      "city": "New York",
      "zipcode": "10001"
    }
  }
}
```
Iska internally format hoga:  
```
user.name → "John"  
user.address.city → "New York"  
user.address.zipcode → "10001"  
```
---

## **6. Comparison: Object vs. Nested Data Type**  <a id="6"></a>

| Feature               | Object          | Nested |
|----------------------|----------------|--------|
| Stored as Hidden Docs? | No             | Yes    |
| Relationship Maintained? | No             | Yes    |
| Query Complexity     | Simple         | Complex |
| Performance         | Faster         | Slower |

---

## **7. Conclusion & Key Takeaways**  <a id="7"></a>
✅ **Basic data types** include `text`, `keyword`, `numeric`, `boolean`, and `date`.  
✅ **Specialized data types** like `ip`, `geo_point`, and `completion` are used for specific purposes.  
✅ **Object data type** is useful for JSON objects but loses relationships when storing arrays of objects.  
✅ **Nested data type** is used to maintain relationships in arrays of objects but is slower in performance.  
✅ **Apache Lucene does not support objects**, so Elasticsearch flattens them for indexing.  


## **8. Example of Data Types During Mapping**  <a id="8"></a>

### **1. Employee Index Mapping Create Karna**
```json
PUT employee
{
  "mappings": {
    "properties": {
      "name": {
        "type": "object",
        "properties": {
          "firstname": { "type": "text" },
          "lastname": { "type": "text" }
        }
      },
      "email": { "type": "keyword" },
      "dob": { "type": "date" },
      "experience": {
        "type": "nested",
        "properties": {
          "company_name": { "type": "text" },
          "duration_in_months": { "type": "integer" }
        }
      },
      "current_salary": { "type": "long" },
      "on_notice_period": { "type": "boolean" }
    }
  }
}
```

---

### **2. Mapping Print Karna**
```json
GET employee/_mapping
```
**Output (mapping structure):**
```json
{
  "employee": {
    "mappings": {
      "properties": {
        "name": {
          "properties": {
            "firstname": { "type": "text" },
            "lastname": { "type": "text" }
          }
        },
        "email": { "type": "keyword" },
        "dob": { "type": "date" },
        "experience": {
          "type": "nested",
          "properties": {
            "company_name": { "type": "text" },
            "duration_in_months": { "type": "integer" }
          }
        },
        "current_salary": { "type": "long" },
        "on_notice_period": { "type": "boolean" }
      }
    }
  }
}
```

---

### **3. Employee Document Insert Karna**
```json
POST employee/_doc/1
{
  "name": {
    "firstname": "Amit",
    "lastname": "Sharma"
  },
  "email": "amit.sharma@example.com",
  "dob": "1990-05-15",
  "experience": [
    {
      "company_name": "Google",
      "duration_in_months": 24
    },
    {
      "company_name": "Microsoft",
      "duration_in_months": 36
    }
  ],
  "current_salary": 3200000,
  "on_notice_period": true
}
```


---
[Reference Video Link](https://youtu.be/KUYuXHJTGUc?si=Em_spYGvXaxCWcph)
---