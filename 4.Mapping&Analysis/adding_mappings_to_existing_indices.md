# 📌 Adding Mappings to Existing Indices in Elasticsearch  

## 📖 **Table of Contents**  

1️⃣ **[Mappings in Elasticsearch: Quick Recap](#1)** 🏗️  
2️⃣ **[Why Add Mapping to an Existing Index?](#2)** ❓  
3️⃣ **[Adding a New Field Using Mapping API](#3)** 🛠️  
4️⃣ **[Example: Adding "created_at" Field](#4)** 📝  
5️⃣ **[Verifying the New Mapping](#5)** 🔍  
6️⃣ **[Why Elasticsearch Doesn’t Store Timestamps by Default?](#6)** ⏳  
7️⃣ **[Edge Cases & Best Practices](#7)** ⚠️  
8️⃣ **[Conclusion](#8)** 🎯  

---  

## 1️⃣ 🏗️ **Mappings in Elasticsearch: Quick Recap** <a id="1"></a>  
Mappings define **structure aur data types** jo kisi index ke documents ke fields ke liye use honge. Agar hum koi field bina mapping ke insert karein, toh Elasticsearch automatically ek mapping generate kar deta hai **(Dynamic Mapping)**.  

Lekin agar hum chahte hain ki **kisi field ka specific data type ho**, toh humein manually mapping define karni padti hai.  

Example:  
```json
{
  "mappings": {
    "properties": {
      "title": { "type": "text" },
      "price": { "type": "double" }
    }
  }
}
```

Ye tabhi kaam karega jab **naya index create ho raha ho**. Lekin **agar index pehle se bana ho**, toh? 🤔  

---  

## 2️⃣ ❓ **Why Add Mapping to an Existing Index?** <a id="2"></a>  
Maan lo humne pehle ek index `"reviews"` banaya jisme sirf review content save ho raha hai:  
```json
{
  "mappings": {
    "properties": {
      "review_text": { "type": "text" },
      "rating": { "type": "integer" }
    }
  }
}
```
Lekin ab humein realize hota hai ki **humein timestamp bhi store karna chahiye** jab review likha gaya.  
Problem yeh hai ki hum index ko **delete nahi kar sakte**, warna data delete ho jayega.  

👉 Solution: **Mapping API ka use karke existing index me field add karna**  

---

## 3️⃣ 🛠️ **Adding a New Field Using Mapping API** <a id="3"></a>  
**PUT method ka use hota hai aur Mapping API ke through "properties" key specify karni hoti hai.**  

⚠ **Note:** Mapping update karte time `"mappings"` key specify **nahi** karni hoti.  

✅ **Correct way:**  
```json
PUT reviews/_mapping
{
  "properties": {
    "created_at": {
      "type": "date"
    }
  }
}
```

❌ **Incorrect way:**  
```json
PUT reviews/_mapping
{
  "mappings": {  
    "properties": {
      "created_at": { "type": "date" }
    }
  }
}
```
⚠ **Yeh galat hai kyunki "mappings" key sirf index create karte time use hoti hai.**  

---

## 4️⃣ 📝 **Example: Adding "created_at" Field** <a id="4"></a>  
Maan lo humare `"reviews"` index ka structure pehle kuch aisa hai:  

```json
GET reviews/_mapping
```
🔽 **Output (before update):**  
```json
{
  "reviews": {
    "mappings": {
      "properties": {
        "review_text": { "type": "text" },
        "rating": { "type": "integer" }
      }
    }
  }
}
```

Ab hum `"created_at"` field add karenge:  

```json
PUT reviews/_mapping
{
  "properties": {
    "created_at": { "type": "date" }
  }
}
```
📌 **Yeh request successfully execute ho jayegi agar index already bana hai.**  

---

## 5️⃣ 🔍 **Verifying the New Mapping** <a id="5"></a>  
Ab check karne ke liye ki naya field mapping me add hua ya nahi:  
```json
GET reviews/_mapping
```
🔽 **Output (after update):**  
```json
{
  "reviews": {
    "mappings": {
      "properties": {
        "review_text": { "type": "text" },
        "rating": { "type": "integer" },
        "created_at": { "type": "date" }
      }
    }
  }
}
```
🎯 **Dekha! Ab "created_at" field bhi mapping me aa gaya hai!**  

---

## 6️⃣ ⏳ **Why Elasticsearch Doesn’t Store Timestamps by Default?** <a id="6"></a>  
Elasticsearch **automatically document index hone ka time save nahi karta**, kyunki:  
1. **Performance impact hota hai** agar har document ke liye extra field store ki jaye.  
2. Har use-case me timestamp ki zaroorat nahi hoti.  
3. Agar chahiye toh hum **manual "created_at" field add kar sakte hain.**  

👉 **Agar hum automatically timestamp store karna chahte hain, toh yeh kar sakte hain:**  
```json
{
  "properties": {
    "created_at": {
      "type": "date",
      "format": "strict_date_optional_time||epoch_millis"
    }
  }
}
```
📌 `"epoch_millis"` format se hum timestamps milliseconds me bhi store kar sakte hain.  

---

## 7️⃣ ⚠️ **Edge Cases & Best Practices** <a id="7"></a>  
✅ **DOs:**  
✔ Mapping update karte waqt **sirf "properties" key specify karein.**  
✔ Timestamp ko store karne ke liye **"date" type use karein.**  
✔ Date format specify karein agar different formats accept karne hain.  

🚫 **DON'Ts:**  
❌ Existing field ka data type change nahi kar sakte (immutable hota hai).  
❌ `"mappings"` key include nahi karni existing index me field add karne ke liye.  
❌ Bina verify kiye production me mapping update mat karein!  

---

## 8️⃣ 🎯 **Conclusion**  <a id="8"></a>
- **Existing index me naye fields add karne ke liye Mapping API ka use hota hai.**  
- **"mappings" nahi, sirf "properties" key specify karni hoti hai.**  
- **"created_at" jaisa timestamp field manually add karna padta hai.**  
- **Mapping update hone ke baad GET request se verify karna chahiye.**  

---

