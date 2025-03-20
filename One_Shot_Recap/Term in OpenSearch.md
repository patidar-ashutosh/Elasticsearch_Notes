### **Term Query in OpenSearch**  

#### 🔹 **Introduction**  
Term Query ek low-level query hoti hai jo exact match ke liye use hoti hai. Ye sirf **exact value** ko search karti hai bina kisi analysis ke. Agar tum ek field me exact word ya phrase match karwana chahte ho, to **term query** best option hai.

---

## **📌 Table of Contents**
1. [Term Query Kya Hai?](#1)
2. [Example Index & Documents](#2)
3. [Basic Term Query Example](#3)
4. [Case Sensitivity in Term Query](#4)
5. [Term Query vs Match Query](#5)
6. [Expected Output](#6)
7. [Key Takeaways](#7)

---

## **1️⃣ Term Query Kya Hai?** <a id="1"></a>  
**Term Query** OpenSearch me ek **exact matching** ke liye use hoti hai. Jab tum is query ka use karoge, to OpenSearch **stemming, tokenization ya lowercasing** jaise kisi bhi analysis ko apply nahi karega. Iska matlab hai ki jo value store ki gayi hai, query me bhi wahi exact same honi chahiye.

---

## **2️⃣ Example Index & Documents** <a id="2"></a>  
Pehle hum ek index create karte hain **products** naam ka aur uspe kuch documents insert karte hain:

### ✅ **Index Creation with Mapping**
```json
PUT products
{
  "mappings": {
    "properties": {
      "name": { "type": "keyword" },
      "category": { "type": "text" },
      "price": { "type": "double" }
    }
  }
}
```
⚡ **Explanation:**  
- `name` field ko **keyword** banaya gaya hai, taaki hum uspe **term query** laga sakein.  
- `category` ko **text** rakha hai, jo analyze hoga.  
- `price` ek numeric field hai.

---

### ✅ **Insert Sample Documents**
```json
POST products/_bulk
{ "index": { "_id": 1 } }
{ "name": "iPhone 13", "category": "Electronics", "price": 799.99 }
{ "index": { "_id": 2 } }
{ "name": "Samsung Galaxy S21", "category": "Electronics", "price": 699.99 }
{ "index": { "_id": 3 } }
{ "name": "Sony Headphones", "category": "Accessories", "price": 199.99 }
```
⚡ **Explanation:**  
Humne ek bulk request bheji jisme 3 products store ho gaye.

---

## **3️⃣ Basic Term Query Example** <a id="3"></a>  
Ab hum **exact match** ke liye **term query** ka use karenge:

### 🔍 **Search for "iPhone 13"**
```json
GET products/_search
{
  "query": {
    "term": {
      "name": {
        "value": "iPhone 13"
      }
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
      {
        "_id": "1",
        "_source": {
          "name": "iPhone 13",
          "category": "Electronics",
          "price": 799.99
        }
      }
    ]
  }
}
```
⚡ **Explanation:**  
- Kyunki `name` ek **keyword field** hai, ye **exact match** karega aur `"iPhone 13"` mil jayega.

---

## **4️⃣ Case Sensitivity in Term Query** <a id="4"></a>  
Agar tum **lowercase ya uppercase change** kar doge, to match nahi karega:

### ❌ **Search for "iphone 13" (Lowercase)**
```json
GET products/_search
{
  "query": {
    "term": {
      "name": {
        "value": "iphone 13"
      }
    }
  }
}
```
### **🔴 Expected Output**
```json
{
  "hits": {
    "total": { "value": 0 },
    "hits": []
  }
}
```
⚠ **Warning:**  
- `name` ek **keyword field** hai, jo case-sensitive hoti hai.  
- `"iPhone 13"` aur `"iphone 13"` alag-alag values hain.

✅ **Solution:** Agar tumhe **case-insensitive search** chahiye, to **match query** ya **normalizer** ka use karo.

---

## **5️⃣ Term Query vs Match Query** <a id="5"></a>  

| Feature          | Term Query | Match Query |
|-----------------|-----------|------------|
| **Analysis**    | ❌ No     | ✅ Yes |
| **Case-Sensitive** | ✅ Yes | ❌ No |
| **Partial Match** | ❌ No | ✅ Yes |
| **Best for**     | IDs, exact values | Full-text search |

**Example:**  
- Agar tumhe `"iPhone"` word ko search karna hai to **match query** use karo.  
- Agar tumhe **exact match** chahiye to **term query** best hai.

---

## **6️⃣ Expected Output Summary** <a id="6"></a>  
| Query | Expected Output |
|-------|---------------|
| `term` `"iPhone 13"` | ✅ Found |
| `term` `"iphone 13"` | ❌ Not Found |
| `match` `"iphone 13"` | ✅ Found (if analyzed) |

---

## **7️⃣ Key Takeaways** <a id="7"></a>  
✔ **Term Query** sirf **exact match** ke liye use hoti hai.  
✔ **Keyword fields** case-sensitive hoti hain, isliye lower/uppercase ka farq padta hai.  
✔ **Match query** natural language search ke liye better hai.  
✔ **IDs, fixed values ya enums** search karne ke liye **term query** best option hai.

---

