## **Understanding Mapping in Elasticsearch**  

Elasticsearch me **mapping** ek important concept hai jo batata hai ki documents ka structure kaisa hoga, kaunse fields honge, aur unka **data type** kya hoga. Yeh concept relational databases ke **table schema** jaisa hota hai. Mapping ka sahi tarike se samajhna **efficient search** aur **data indexing** ke liye zaroori hai.  

---

## **📌 Table of Contents**  
1️⃣ [Introduction to Mapping](#1)  
2️⃣ [Why Mapping is Important?](#2)  
3️⃣ [Mapping vs SQL Table Schema](#3)  
4️⃣ [Explicit vs Dynamic Mapping](#4)  
5️⃣ [How Mapping Works? (Examples)](#5)  
6️⃣ [Combining Explicit and Dynamic Mapping](#6)  
7️⃣ [How to Check Index Mapping](#7)  
8️⃣ [Best Practices for Mapping](#8)  
9️⃣ [Conclusion](#9)  

---

## **1️⃣ Introduction to Mapping**  <a id="1"></a>
💡 **Mapping** define karta hai ki:  
✔ Documents ka **structure** kya hoga?  
✔ Kaunse **fields** honge?  
✔ **Fields ke data types** kya honge?  

🔹 **Example:**  
Agar tum ek **"products"** index bana rahe ho jisme **"name"**, **"price"**, aur **"in_stock"** fields hai, toh mapping kuch is tarah se hoga:  

```json
PUT /products
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "price": { "type": "float" },
      "in_stock": { "type": "boolean" }
    }
  }
}
```
🔹 Isme humne bataya ki:  
- `"name"` ek **text field** hai.  
- `"price"` ek **floating number** hoga.  
- `"in_stock"` ek **boolean** field hai.  

---

## **2️⃣ Why Mapping is Important?**  <a id="2"></a>
✔ **Faster Search:** Elasticsearch ko pata hota hai ki kaunsa data kis format me hai, toh searching fast hoti hai.  
✔ **Optimized Storage:** Proper mapping se data efficiently store hota hai.  
✔ **Correct Query Execution:** Agar fields ka incorrect type ho toh queries error de sakti hain.  

🔹 **Example:**  
Agar tum `"price"` ko text ke form me store karoge, toh tum numeric range queries nahi chala sakoge.  

```json
GET products/_search
{
  "query": {
    "range": {
      "price": {
        "gte": 1000
      }
    }
  }
}
```
**Galat mapping hone par yeh query fail ho jayegi!**  

---

## **3️⃣ Mapping vs SQL Table Schema**  <a id="3"></a>
| Feature | SQL Table Schema | Elasticsearch Mapping |
|---------|-----------------|----------------------|
| Data Storage | Rows & Columns | JSON Documents |
| Schema | Fixed Schema | Flexible Schema |
| Data Types | Strongly Typed | Supports Dynamic Typing |
| Indexing | Primary & Secondary Indexes | Inverted Index |

🔹 **Key Difference:**  
- SQL databases me **schema rigid hota hai**, jabki Elasticsearch me **mapping flexible** hota hai.  
- Elasticsearch **nested documents** aur **full-text search** ko optimize karta hai.  

---

## **4️⃣ Explicit vs Dynamic Mapping**  <a id="4"></a>

## 🔹 **Explicit Mapping:**  
- Tum **khud manually** bata sakte ho ki kaunse fields honge aur unka data type kya hoga.  
- Yeh **better control** deta hai aur **performance optimize** karta hai.  

💻 **Example:**  
```json
PUT products
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "price": { "type": "float" },
      "created_at": { "type": "date" }
    }
  }
}
```

---

💻 **Example Of Nested Explicit Mapping**  
#### 🛒 **Scenario:**  
Hum ek **"products"** index create karenge jisme har product ke multiple **reviews** honge.  
✔ **Product** ke andar **reviews** ek **nested object** ke form me store honge.  
✔ Har review me **username, rating aur comment** hoga.  

```json
PUT products
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "category": { "type": "keyword" },
      "price": { "type": "float" },
      "reviews": {
        "type": "nested", 
        "properties": {
          "username": { "type": "keyword" },
          "rating": { "type": "integer" },
          "comment": { "type": "text" }
        }
      }
    }
  }
}
```
🔹 Yahan `"reviews"` field ko **"nested" type** diya gaya hai.  
🔹 **"reviews" ke andar alag alag properties** (`username`, `rating`, `comment`) define ki gayi hain.  

---

💻 **Example Of Same Nested Mapping in Dot Notation**  

```json
PUT products
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "category": { "type": "keyword" },
      "price": { "type": "float" },
      "reviews": { "type": "nested" },
      "reviews.username": { "type": "keyword" },
      "reviews.rating": { "type": "integer" },
      "reviews.comment": { "type": "text" }
    }
  }
}
```

### **🛠 Difference Between Both Approaches**
| Approach | Pros | Cons |
|----------|------|------|
| **Normal (Explicit JSON)** | Structured, easier to read | More lines of code |
| **Dot Notation** | Shorter, easier to write | Might be harder to read for complex structures |

🚀 **Conclusion:**  
Haan, tum **dot notation** ka use kar sakte ho, aur Elasticsearch isse correctly parse karega.  
Dono approaches sahi hain, bas readability aur preference pe depend karta hai! 🔥


## 🔹 **Dynamic Mapping:**  
- Agar tumne **explicit mapping define nahi ki**, toh Elasticsearch **automatically data type detect** kar lega.  
- Yeh **flexible hota hai** lekin kabhi-kabhi unexpected behavior ho sakta hai.  

💻 **Example:**  
```json
POST products/_doc/1
{
  "name": "Laptop",
  "price": 55000,
  "in_stock": true
}
```
Yahan `"price"` ko **float**, `"name"` ko **text**, aur `"in_stock"` ko **boolean** automatically assign kiya jayega.  

---

## **5️⃣ Different Way Of Doing Dynamic Mapping**  <a id="5"></a>
Elasticsearch me **`dynamic` mapping** ke **3 possible values** hoti hain:  

1️⃣ **`true` (default)** → **Naye fields automatically add ho jate hain.**  
2️⃣ **`false`** → **Naye fields ignore ho jate hain, lekin error nahi aata.**  
3️⃣ **`"strict"`** → **Naye fields pe error aata hai, document insert nahi hota.**  

---

## ✅ **1. `dynamic: true` (Default Behavior)**  

🔹 **New fields automatically index ho jate hain.**  

```json
PUT my_index
{
  "mappings": {
    "dynamic": true
  }
}
```

👨‍💻 **Insert a document with a new field (`city`)**  

```json
POST my_index/_doc/1
{
  "name": "Alice",
  "age": 25,
  "city": "New York"
}
```

📌 **Result:** `"city"` field mapping me automatically add ho gaya aur index bhi ho gaya.  

---

## ❌ **2. `dynamic: false` (Ignore New Fields)**  

🔹 **Naye fields ignore ho jate hain, store nahi hote, but error nahi aata.**  

```json
PUT my_index
{
  "mappings": {
    "dynamic": false,
    "properties": {
      "name": { "type": "text" },
      "age": { "type": "integer" }
    }
  }
}
```

👨‍💻 **Insert a document with a new field (`city`)**  

```json
POST my_index/_doc/1
{
  "name": "Alice",
  "age": 25,
  "city": "New York"
}
```

📌 **Result:**  
- Document successfully index ho jayega.  
- **"city" field document me store hoga, lekin inverted index me store nahi hoga or search me bhi nahi aayega.**  

---

## 🚫 **3. `dynamic: "strict"` (Error on New Fields)**  

🔹 **Agar koi naya field aaye, to error throw hoga.**  

```json
PUT my_index
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

👨‍💻 **Insert a document with a new field (`city`)**  

```json
POST my_index/_doc/1
{
  "name": "Alice",
  "age": 25,
  "city": "New York"
}
```

📌 **Result:** ❌ **Error aayega:**  

```json
{
  "error": {
    "reason": "mapping set to strict, dynamic introduction of [city] within [my_index] is not allowed"
  }
}
```

🚀 **Conclusion:**  
- `true` → **Naya field automatically add ho jata hai.**  
- `false` → **Naya field ignore hota hai, error nahi aata.**  
- `"strict"` → **Naya field pe error aata hai, document insert nahi hota.**  

---

## **6️⃣ Combining Explicit and Dynamic Mapping**  <a id="6"></a>
✔ Tum **explicit mapping aur dynamic mapping dono** combine kar sakte ho.  
✔ Example: Tum important fields **manually define** kar sakte ho aur baaki fields ko **dynamically detect** hone de sakte ho.  

💻 **Example:**  
```json
PUT products
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "price": { "type": "float" }
    }
  }
}
```
Agar tum **extra fields** daalte ho jo define nahi kiye gaye hain, toh Elasticsearch **unko automatically detect** karega.  

---

## **7️⃣ How to Check Index Mapping**  <a id="7"></a>
Elasticsearch me **index mapping check** karne ke liye aap **`_mapping` API** ka use kar sakte ho.  

## 🔍 **1. Poore Index Ki Mapping Dekhna**  
Agar aapko kisi **specific index** ki **poori mapping** dekhni hai, to yeh query run karo:  

```json
GET my_index/_mapping
```

👀 **Example Output:**  
```json
{
  "my_index": {
    "mappings": {
      "properties": {
        "name": { "type": "text" },
        "age": { "type": "integer" },
        "email": { "type": "keyword" }
      }
    }
  }
}
```
📌 **Isme aap dekh sakte ho ki `name` field `text`, `age` field `integer`, aur `email` field `keyword` type ka hai.**  

---

## 🔍 **2. Specific Field Ki Mapping Dekhna**  
Agar aapko **sirf ek specific field** ki mapping dekhni hai, to yeh query use karo:  

```json
GET my_index/_mapping/field/name
```

👀 **Example Output:**  
```json
{
  "my_index": {
    "mappings": {
      "name": {
        "full_name": "name",
        "mapping": {
          "name": { "type": "text" }
        }
      }
    }
  }
}
```
📌 **Yeh confirm karega ki `name` field ka type `text` hai.**  

---

## 🔍 **3. Saare Index Ki Mapping Dekhna**  
Agar aapko **saare indexes** ki **mapping** dekhni ho, to yeh query use kar sakte ho:  

```json
GET _all/_mapping
```
Ya agar aapke Elasticsearch ka **version 7+** hai, to isko use karein:  

```json
GET _mapping
```

👀 **Example Output (Multiple Indexes ke liye):**  
```json
{
  "index_1": { "mappings": { /* mapping details */ } },
  "index_2": { "mappings": { /* mapping details */ } }
}
```

📌 **Isse aapko sabhi indexes ki mapping ek saath mil jayegi.**  

---

### ✅ **Conclusion:**  
- **Specific index mapping:** `GET my_index/_mapping`  
- **Specific field mapping:** `GET my_index/_mapping/field/field_name`  
- **All indexes mapping:** `GET _mapping`  

🔥 **Ab aap Elasticsearch me index ki mapping easily check kar sakte ho!** 🚀

---

## **8️⃣ Best Practices for Mapping**  <a id="8"></a>
✔ **Explicit Mapping Use Karo:** Agar tumhe fields pata hain toh manually mapping define karo.  
✔ **Keyword vs Text:** Agar tumhe exact matches chahiye toh `"keyword"` use karo, warna `"text"` use karo.  
✔ **Date Fields Define Karo:** `"date"` type explicitly define karo warna Elasticsearch galat format detect kar sakta hai.  
✔ **Dynamic Mapping Strict Mode Me Rakho:** **Unwanted fields prevent karne ke liye** `"dynamic": "strict"` use karo.  

---

## **9️⃣ Conclusion**  <a id="9"></a>
🔹 **Mapping** Elasticsearch ka ek **important concept** hai jo documents ka **structure define** karta hai.  
🔹 **Explicit Mapping** tumhe **better control** aur **optimized performance** deta hai.  
🔹 **Dynamic Mapping** flexible hai, lekin unpredictable behavior ho sakta hai.  
🔹 **Best practices** follow karke tum apni **indexing aur searching ko optimize** kar sakte ho.  

✔ **Agar mapping sahi set ki gayi ho toh Elasticsearch me querying fast aur efficient hoti hai!** 🚀  

---

## **📜 Summary in One Line:**  
**Mapping = Elasticsearch ka table schema + Data types + Efficient searching ka foundation!** 🔥

---
[Reference Video Link 1](https://youtu.be/IVnYv_cdIjw?si=A5BS47eXj6nlpDLA)
[Reference Video Link 2](https://youtu.be/8BWqRFr2Pj0?si=A6wSKRHNYTeAu2tX)
---