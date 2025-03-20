# 📌 Terms Lookup Mechanism

## 📖 Table of Contents  

1️⃣ **🔍 Terms Query Kya Hoti Hai?**  
2️⃣ **📌 Terms Lookup Mechanism Kya Hai?**  
3️⃣ **🔄 Terms Query vs Terms Lookup Query**  
4️⃣ **🛠️ Practical Example with JSON Queries**  
5️⃣ **📊 Expected Output Explanation**  
6️⃣ **⚡ Performance Benefits of Terms Lookup**  
7️⃣ **💡 Real-World Use Cases**  
8️⃣ **🔚 Summary & Key Takeaways**  

---

## 1️⃣ 🔍 Terms Query Kya Hoti Hai?  

**Terms query** ka use hota hai jab tumhe **multiple values** ke basis pe documents filter karne hote hain. Ye **match query** jaisa hi hota hai, bas yeh ek se zyada values ko support karta hai.  

### **Example:**  
Maan lo tumhe **specific user IDs** ke stories fetch karni hain.  

```json
{
  "query": {
    "terms": {
      "user_id": [2, 3, 5]
    }
  }
}
```

👉 **Ye query unhi stories ko fetch karegi jinme `user_id` 2, 3, ya 5 hoga.**  

---  

## 2️⃣ 📌 Terms Lookup Mechanism Kya Hai?  

Agar hume **500+ user IDs** ko query karna ho, to manually sabko list karna feasible nahi hoga. **Terms lookup mechanism** Elasticsearch me dynamically terms retrieve karne ka ek smart way hai.  

✅ **Instead of hardcoding values, hum dynamically ek dusre document se terms fetch kar sakte hain.**  
✅ **Jo terms hume chahiye, wo kisi aur index/document ke andar stored ho sakti hain.**  

---

## 3️⃣ 🔄 Terms Query vs Terms Lookup Query  

Agar hume manually IDs specify karni ho, to **normal terms query** ka use hota hai:  

```json
{
  "query": {
    "terms": {
      "user_id": [2, 3, 5]
    }
  }
}
```

**Par agar hume dynamically kisi aur document se values fetch karni ho, to terms lookup query ka use hota hai:**  

```json
{
  "query": {
    "terms": {
      "user_id": {
        "index": "users",
        "id": "1",
        "path": "following"
      }
    }
  }
}
```

👉 **Yeh Elasticsearch ko batata hai ki `users` index ke andar `id: 1` wale document ke `following` field se values uthani hain.**  

---

## 4️⃣ 🛠️ Practical Example with JSON Queries  

### **Step 1: Users Index Create Karo**  
```json
PUT users
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "following": { "type": "keyword" }
    }
  }
}
```

### **Step 2: Sample Data Insert Karo**  
```json
POST users/_doc/1
{
  "name": "John Doe",
  "following": ["2", "3"]
}

POST users/_doc/2
{
  "name": "Alice",
  "following": ["4", "5"]
}
```

### **Step 3: Stories Index Create Karo**  
```json
PUT stories
{
  "mappings": {
    "properties": {
      "user_id": { "type": "keyword" },
      "content": { "type": "text" }
    }
  }
}
```

### **Step 4: Sample Stories Insert Karo**  
```json
POST stories/_doc/10
{
  "user_id": "2",
  "content": "Story by User 2"
}

POST stories/_doc/11
{
  "user_id": "3",
  "content": "Story by User 3"
}

POST stories/_doc/12
{
  "user_id": "4",
  "content": "Story by User 4"
}
```

### **Step 5: Terms Lookup Query Lagao**  
```json
GET stories/_search
{
  "query": {
    "terms": {
      "user_id": {
        "index": "users",
        "id": "1",
        "path": "following"
      }
    }
  }
}
```

👉 **Yeh query `users` index me `id:1` wale user ke `following` field se IDs uthayegi aur unse related stories fetch karegi.**  

---

## 5️⃣ 📊 Expected Output Explanation  

Agar **John Doe (`id:1`) ke following users (2, 3) hain**, to query ka output kuch aisa hoga:  

```json
{
  "hits": {
    "total": 2,
    "hits": [
      { "_source": { "user_id": "2", "content": "Story by User 2" } },
      { "_source": { "user_id": "3", "content": "Story by User 3" } }
    ]
  }
}
```

👉 **Query ne automatically `users` index me `id:1` ke `following` field se values uthayi aur sirf wahi stories return ki jo `user_id` 2 ya 3 ke hain.**  

---

## 6️⃣ ⚡ Performance Benefits of Terms Lookup  

✅ **Network Calls Reduce Hote Hain:**  
   - Agar hum ye lookup application level pe karein, to pehle ek query se user ka `following` field fetch karna padega.  
   - Phir ek aur query lagani padegi jo in IDs se related documents fetch kare.  
   - **Terms lookup se yeh sab ek hi query me ho jata hai.**  

✅ **Faster Execution:**  
   - Agar 10,000 users follow ho, to manually query bhejna inefficient hoga.  
   - **Terms lookup Elasticsearch ke andar optimized hota hai, jo memory aur CPU efficiently use karta hai.**  

✅ **Reduced Data Transfer:**  
   - Agar ek user **1000 log follow karta hai**, to **client-server ke beech me data transfer bohot heavy ho sakta hai.**  
   - **Terms lookup directly Elasticsearch ke andar process hota hai, jisse network latency reduce hoti hai.**  

---

## 7️⃣ 💡 Real-World Use Cases  

✅ **Social Media Feed:**  
   - **Users index me followers ki list store hoti hai.**  
   - **Terms lookup ka use karke dynamically unke following accounts ke posts fetch kiye ja sakte hain.**  

✅ **E-commerce Wishlist Matching:**  
   - **Users index me wishlist stored hoti hai.**  
   - **Terms lookup ka use karke users ki wishlist ke items ke active offers fetch kiye ja sakte hain.**  

✅ **Personalized News Aggregator:**  
   - **Users index me preferred categories hoti hain.**  
   - **Terms lookup ka use karke dynamically unhi categories se news fetch ho sakti hain.**  

---

## 8️⃣ 🔚 Summary & Key Takeaways  

✅ **Terms Query** ek normal query hai jo manually specify kiye gaye multiple values ko match karti hai.  
✅ **Terms Lookup Mechanism** dynamically ek aur document se terms fetch karne ka smart way hai.  
✅ **Application ke andar multiple queries bhejne ki jagah Elasticsearch me internally optimize query chalti hai.**  
✅ **Agar thousands of terms handle karne hain, to ye approach better hoti hai kyunki network calls aur processing reduce hoti hai.**  
✅ **Performance degrade ho sakti hai agar bohot zyada terms ho, but Elasticsearch ek limit lagata hai (by default 65,000 terms).**  

---

