# 📌 **Using Dot Notation in Field Names**  

Dot notation Elasticsearch me ek shortcut hai jo complex object mappings ko simplify karne me madad karta hai. Ye sirf mapping define karne ke liye nahi, balki queries me bhi kaam aata hai. Is explanation me hum dekhenge ki kaise dot notation mapping aur queries me kaam karta hai, aur iska impact kya hota hai.

---

## 📖 **Table of Contents**  
1️⃣ [Dot Notation Kya Hai?](#1)  
2️⃣ [Traditional Mapping vs Dot Notation](#2)  
3️⃣ [Mapping Example with Dot Notation](#3)  
4️⃣ [Dot Notation in Search Queries](#4)  
5️⃣ [Behind the Scenes: Dot Notation ka Internal Work](#5)  
6️⃣ [Conclusion](#6)  

---

## 1️⃣ 🎯 **Dot Notation Kya Hai?**  <a id="1"></a>
Dot notation ka use Elasticsearch me hierarchical objects ko represent karne ke liye hota hai bina explicitly "properties" define kiye. Iska fayda ye hai ki mapping aur queries zyada readable aur clean dikhte hain.  

Jaise agar ek document me `author` ek object hai, to hum traditionally isko aise define karte:  

```json
{
  "author": {
    "first_name": "John",
    "last_name": "Doe"
  }
}
```
Dot notation ka use karne par, hum ise ek hi level par represent kar sakte hain:  

```json
{
  "author.first_name": "John",
  "author.last_name": "Doe"
}
```

---

## 2️⃣ 🔄 **Traditional Mapping vs Dot Notation**  <a id="2"></a>
Traditional mapping me **"properties"** parameter ka use hota hai, jo objects ko clearly define karta hai.  
Dot notation ise simplify karta hai, lekin internally Elasticsearch ise waise hi process karta hai jaise traditional mapping me hota.  

### 📌 **Traditional Approach:**  
```json
{
  "mappings": {
    "properties": {
      "author": {
        "properties": {
          "first_name": { "type": "text" },
          "last_name": { "type": "text" }
        }
      }
    }
  }
}
```

### 📌 **Dot Notation Approach:**  
```json
{
  "mappings": {
    "properties": {
      "author.first_name": { "type": "text" },
      "author.last_name": { "type": "text" }
    }
  }
}
```

**💡Note:** Elasticsearch is dot notation ko internally **properties** structure me convert kar deta hai.

---

## 3️⃣ 🛠 **Mapping Example with Dot Notation**  <a id="3"></a>
Chalo ek example dekhte hain jisme hum dot notation ka use karke mapping create karenge:  

### **Step 1: Index Create karna with Dot Notation**
```sh
PUT reviews_dot_notation
{
  "mappings": {
    "properties": {
      "author.first_name": { "type": "text" },
      "author.last_name": { "type": "text" }
    }
  }
}
```

### **Step 2: Mapping Retrieve karna**
```sh
GET reviews_dot_notation/_mapping
```
🔹 **Expected Output:**  
```json
{
  "reviews_dot_notation": {
    "mappings": {
      "properties": {
        "author": {
          "properties": {
            "first_name": { "type": "text" },
            "last_name": { "type": "text" }
          }
        }
      }
    }
  }
}
```
**⚠️ Important:**  
Even though humne dot notation use ki thi, Elasticsearch internally isse `properties` structure me convert kar deta hai.

---

## 4️⃣ 🔍 **Dot Notation in Search Queries**  <a id="4"></a>
Dot notation ka use sirf mapping me nahi hota, balki search queries me bhi kiya ja sakta hai.  

### **Example Query: Find Author by First Name**
```sh
GET reviews_dot_notation/_search
{
  "query": {
    "match": {
      "author.first_name": "John"
    }
  }
}
```

🔹 **Expected Output:**
```json
{
  "hits": {
    "total": 1,
    "hits": [
      {
        "_source": {
          "author.first_name": "John",
          "author.last_name": "Doe"
        }
      }
    ]
  }
}
```

Ye query `author.first_name` field me `John` ko search karegi bina kisi nested query ke.

---

## 5️⃣ ⚙️ **Behind the Scenes: Dot Notation ka Internal Work**  <a id="5"></a>
Jab hum dot notation use karte hain, Elasticsearch usse internally **"properties"** structure me convert kar deta hai.  
Lekin jab queries likhte hain, tab hume ise dot notation me hi likhna padta hai.  

**Example:**  
- **Dot Notation Mapping:** `"author.first_name": "text"`
- **Internally Convert Hota Hai:**  
  ```json
  {
    "author": {
      "properties": {
        "first_name": { "type": "text" }
      }
    }
  }
  ```

Eska matlab ye hai ki **index mapping me dot notation kabhi directly nahi dikhega**, par hum ise queries me likh sakte hain.

---

## 6️⃣ ✅ **Conclusion**  <a id="6"></a>
🔹 **Dot notation ek shortcut hai jo mapping aur queries dono me use hota hai.**  
🔹 **Ye mapping ko cleaner aur readable banata hai, particularly jab deeply nested objects ho.**  
🔹 **Elasticsearch internally ise properties structure me convert kar deta hai, isliye mapping retrieve karne par dot notation nahi dikhega.**  
🔹 **Dot notation ka use queries me bhi hota hai, jo ki queries likhne me easy aur efficient banata hai.**  

🚀 **Agar tumhari mapping deeply nested ho to dot notation use karna better rahega, par agar tumhe clear hierarchy dikhani hai to traditional approach better ho sakti hai.**