## Updating Existing Mappings in Elasticsearch

### **📌 Table of Contents**  
1️⃣ [Introduction](#1)  
2️⃣ [Elasticsearch Mapping Basics](#2)  
3️⃣ [Why Can’t We Update Existing Field Mappings?](#3)  
4️⃣ [What Can Be Updated in a Mapping?](#4)  
5️⃣ [Example: Adding the `ignore_above` Parameter](#5)  
6️⃣ [Workaround: How to Change a Field’s Data Type](#6)  
7️⃣ [Reindexing: Step-by-Step Guide](#7)  
8️⃣ [Best Practices](#8)  
9️⃣ [Conclusion](#9)  

---

## 1**🔹 Introduction** <a id="1"></a>
Elasticsearch me ek **field mapping** define karta hai ki kisi document ke ek specific field ka **data type** kya hoga aur uske upar kaunse operations perform ho sakte hain. Lekin agar ek baar mapping set ho gayi, toh usko **badalna possible nahi hota**, sirf kuch specific parameters update kiye ja sakte hain.

Is guide me hum dekhenge:
- Kyon field mapping update nahi ho sakti?
- Kaunse parameters update kiye ja sakte hain?
- Agar ek field ka data type change karna ho toh kaise karein?
- Reindexing process kya hoti hai?

---

## 2**🔹 Elasticsearch Mapping Basics** <a id="2"></a>
Elasticsearch me har **index** ek **mapping** ke saath aata hai jo batata hai ki data ka structure kaisa hoga.

Example:
```json
{
  "mappings": {
    "properties": {
      "product_id": { "type": "long" },
      "name": { "type": "text" }
    }
  }
}
```
Yahan `product_id` field ka type `"long"` hai, jo ki numeric values store karne ke liye use hota hai.

Agar tumhe `product_id` ko **"keyword"** me change karna ho toh kya karoge? 🤔  
**Direct update possible nahi hai!**

---

## 3**🔹 Why Can’t We Update Existing Field Mappings?** <a id="3"></a>
Elasticsearch me ek baar mapping **define hone ke baad change nahi ho sakti**. Iska reason yeh hai ki:
1. **Data Already Indexed** – Elasticsearch ek structured format me data ko store karta hai. Agar tum ek field ka type `long` se `keyword` karna chaaho, toh uska pura data structure change ho jayega.
2. **Data Structures are Different** – Numeric fields (`long`) ko **BKD tree** me store kiya jata hai, jabki text fields (`keyword`) ko **inverted index** me.
3. **Reindexing Required** – Agar tum mapping change karna chaaho, toh **pura index dobara create karna padega aur data migrate karna padega**.
4. **Performance Issues** – Agar Elasticsearch mapping updates allow kare, toh **large-scale clusters crash ho sakte hain** kyunki billions of documents reprocessed honge.

🚨 **Example of Failure:**  
Agar tum yeh query run karoge:
```json
PUT reviews/_mapping
{
  "properties": {
    "product_id": { "type": "keyword" }
  }
}
```
Toh **error aayega**:
```
The provided mapping cannot be applied to an existing field. It is not dynamically updateable.
```

---

## 4**🔹 What Can Be Updated in a Mapping?** <a id="4"></a>
Elasticsearch me **sirf kuch limited parameters** ko update karne ki permission hoti hai. Yeh include karte hain:
| **Parameter**   | **Explanation** |
|---------------|---------------|
| `ignore_above` | Ek specific character length se bade values ko ignore karne ke liye. |
| `coerce` | Elasticsearch automatically values ko convert kare ya nahi. |
| `dynamic` | New fields automatically detect hone chahiye ya nahi. |

---

## 5**🔹 Example: Adding the `ignore_above` Parameter** <a id="5"></a>
Agar ek field ka **data type change nahi ho sakta**, toh kya koi field me **extra constraints** add kiye ja sakte hain?  
Haan! Kuch parameters jaise `ignore_above` update kiye ja sakte hain.

### **✅ Example:**
```json
PUT reviews/_mapping
{
  "properties": {
    "author": {
      "properties": {
        "email": {
          "type": "keyword",
          "ignore_above": 256
        }
      }
    }
  }
}
```
✅ Isme humne **email field** me `ignore_above: 256` add kiya.  
✅ Iska matlab: **Agar koi email 256 characters se lambi hogi, toh Elasticsearch usko index nahi karega**.

---

## 6**🔹 Workaround: How to Change a Field’s Data Type** <a id="6"></a>
Agar tumhe **"product_id"** ko `long` se `keyword` me change karna ho, toh iska **direct solution nahi hai**. Tumhe **naya index banana padega aur purana data migrate karna padega.**

### **🚀 Solution: Reindexing**
1. **Naya index banao jisme correct mapping ho**
2. **Purane index ka data naye index me migrate karo**
3. **Old index ko delete kar do**

---

## 7**🔹 Reindexing: Step-by-Step Guide** <a id="7"></a>
Maan lo, humne `product_id` ka type `long` se `keyword` karna hai.

### **Step 1️⃣: Naya Index Create Karo**
```json
PUT reviews_v2
{
  "mappings": {
    "properties": {
      "product_id": { "type": "keyword" },
      "name": { "type": "text" }
    }
  }
}
```

### **Step 2️⃣: Data Migrate Karo (Reindex API)**
```json
POST _reindex
{
  "source": { "index": "reviews" },
  "dest": { "index": "reviews_v2" }
}
```
✅ Yeh pura data **`reviews` se `reviews_v2` me migrate** kar dega.

### **Step 3️⃣: Old Index Delete Karo**
```json
DELETE reviews
```

### **Step 4️⃣: Alias Set Karo (Optional)**
Agar tum **naya index use karna chahte ho bina queries change kiye**, toh alias set kar sakte ho:
```json
POST _aliases
{
  "actions": [
    { "add": { "index": "reviews_v2", "alias": "reviews" } }
  ]
}
```

---

## 8**🔹 Best Practices** <a id="8"></a>
✅ **Pehle se data types soch ke define karo** – Baad me change karna bahut mushkil hota hai.  
✅ **Reindexing ke liye downtime plan karo** – Bada dataset ho toh reindexing slow ho sakti hai.  
✅ **Aliasing ka use karo** – Index ko dynamically switch karne ke liye aliases use karo.  
✅ **Mapping update test karo** – Production me change karne se pehle **staging cluster** pe test karo.  

---

## 9**🔹 Conclusion** <a id="9"></a>
- **Elasticsearch mapping ek baar set hone ke baad update nahi hoti** (except kuch parameters).
- **Agar ek field ka data type change karna hai, toh reindexing karni padegi**.
- **Reindexing ka process simple hai, par large datasets ke liye downtime aur performance issues ka dhyan rakhna zaroori hai**.

