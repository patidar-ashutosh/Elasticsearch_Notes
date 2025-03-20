### 📖 **Understanding Type Coercion in Elasticsearch**  

Type coercion Elasticsearch me ek process hai jisme ek field ka data type automatically adjust ho jata hai jab hum indexing karte hain. Ye feature kabhi helpful hota hai, magar kabhi kabhi problems bhi create kar sakta hai.  

---

## 📌 **Table of Contents**
1️⃣ [Type Coercion Kya Hota Hai?](#1)  
2️⃣ [Type Coercion Kaise Kaam Karta Hai?](#2)  
3️⃣ [Examples of Type Coercion](#3)  
4️⃣ [Coercion Kab Fail Hota Hai?](#4)  
5️⃣ [Source Document vs. Stored Data](#5)  
6️⃣ [Type Coercion Disable Kaise Karein?](#6)  
7️⃣ [Summary](#7)  

---

## 1️⃣ **Type Coercion Kya Hota Hai?** <a id="1"></a>  

🔹 Type coercion ek process hai jisme Elasticsearch automatically ek incorrect data type ko correct data type me convert karne ki koshish karta hai.  
🔹 Ye tab hota hai jab hum kisi document me ek field ka galat data type provide karte hain, magar Elasticsearch usse convert kar sakta hai.  
🔹 **Example:** Agar hum kisi numeric field me `"7.4"` string bhejte hain, toh Elasticsearch isse float me convert kar sakta hai.  

---

## 2️⃣ **Type Coercion Kaise Kaam Karta Hai?** <a id="2"></a>  

🔹 Jab hum kisi document ko index karte hain, toh Elasticsearch mapping ke hisaab se uska data type check karta hai.  
🔹 Agar humne mapping nahi banayi, toh **dynamic mapping** data type assign karta hai.  
🔹 Jab ek field ka data type mapping se match nahi karta, tab type coercion activate hota hai.  

---

## 3️⃣ **Examples of Type Coercion** <a id="3"></a>  

### ✅ **Example 1: String se Float Conversion**  
Agar `"price"` field **float** me store ho rahi hai, magar hum string bhejte hain:

```json
PUT my_index/_doc/1
{
  "price": "7.4"
}
```

🟢 **Elasticsearch isko accept karega** kyunki `"7.4"` ek valid numeric string hai.  
🔄 **Converted Value:** `"7.4"` ko float `7.4` me convert kar diya jayega.  

---

### ✅ **Example 2: Float se Integer Conversion**  
Agar `"age"` field **integer** me store ho rahi hai, magar hum float bhejte hain:

```json
PUT my_index/_doc/2
{
  "age": 25.8
}
```

🟢 **Elasticsearch isko accept karega**, magar `25.8` truncate hokar `25` ban jayega.  
🔄 **Converted Value:** `25.8` → `25`  

---

## 4️⃣ **Coercion Kab Fail Hota Hai?** <a id="4"></a>  

🚫 **Invalid Conversion Example:**  
Agar hum ek string provide karte hain jo number me convert nahi ho sakti:

```json
PUT my_index/_doc/3
{
  "price": "abc"
}
```
❌ **Error:** `"abc"` float me convert nahi ho sakta, toh Elasticsearch error throw karega.  

---

## 5️⃣ **Source Document vs. Stored Data** <a id="5"></a>  

🟢 Jab hum document ko retrieve karte hain (`_source` se), toh hume wahi value dikhti hai jo humne bheji thi.  
🛠️ Magar **internally** Elasticsearch Lucene index me coerced value store karta hai.  

Example:  

```json
GET my_index/_doc/1
```

👀 **Output:**  
```json
{
  "_source": {
    "price": "7.4"
  }
}
```
💡 **Yeh string lag rahi hai, magar internally yeh float `7.4` ke roop me store hoti hai.**  

---

## 6️⃣ **Type Coercion Disable Kaise Karein?** <a id="6"></a>  

🔹 Agar aap chahte ho ki Elasticsearch **galat data type reject kare** instead of automatically convert karne ke, toh aap coercion disable kar sakte ho.  

### ❌ **Disable Type Coercion**
```json
PUT my_index
{
  "mappings": {
    "properties": {
      "price": {
        "type": "float",
        "coerce": false
      }
    }
  }
}
```

📌 **Ab agar koi galat data type aayega, toh Elasticsearch error dega.**  

---

## 7️⃣ **Summary** <a id="7"></a>  

🔹 **Type coercion** Elasticsearch me ek automatic process hai jo incorrect data type ko correct data type me convert karta hai.  
🔹 Yeh **strings to numbers**, **floats to integers**, jaise cases me kaam karta hai.  
🔹 Magar agar conversion possible nahi ho, toh error aa jata hai.  
🔹 **Coercion ko disable bhi kiya ja sakta hai** agar aapko strict data validation chahiye.  

[🔝 Back to Top](#📌-table-of-contents)

---
[Reference Video Link](https://youtu.be/PlX2r054oIQ?si=p-cKsuIgmWFT3Zv8)
---