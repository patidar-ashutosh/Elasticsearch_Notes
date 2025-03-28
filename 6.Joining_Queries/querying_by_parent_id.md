## 📜 **Table of Contents**  

1️⃣ **[Parent ID Query Kya Hai?](#1) 🔍**  
2️⃣ **[Basic Query Syntax](#2) 📝**  
3️⃣ **[Query Ka Expected Output](#3) 📊**  
4️⃣ **[Routing Ka Role](#4) 📌**  
5️⃣ **[Parent ID Query vs Has Parent Query](#5) ⚖️**  
6️⃣ **[Real-World Use Cases](#6) 🌍**  
7️⃣ **[Key Takeaways](#7) 🎯**

---

## 1️⃣ **Parent ID Query Kya Hai?** 🔍  <a id="1"></a>

Elasticsearch me agar tumhe kisi **parent document ke ID** ke basis pe uske **child documents** retrieve karne hain, to `parent_id` query use hoti hai.  

💡 Example:  
- **Department (Parent)** → **Employees (Children)**  
- Agar tumhe ek **department ID = 1** ke **saare employees** dikhane hain, to **parent_id query** ka use karoge.  

---

## 2️⃣ **Basic Query Syntax** 📝  <a id="2"></a>

Chalo ek query likhte hain jo **department ID = 1** ke saare employees ko return karegi:  

```json
GET my_index/_search
{
  "query": {
    "parent_id": {
      "type": "employee",
      "id": "1"
    }
  }
}
```

🔹 **`type: "employee"`** → Ye batata hai ki hume **child documents (employees)** chahiye.  
🔹 **`id: "1"`** → Ye batata hai ki hume **department ID = 1** ke employees chahiye.  

---

## 3️⃣ **Query Ka Expected Output** 📊  <a id="3"></a>

Agar **department ID = 1** ke andar **4 employees** hain, to response kuch aisa aayega:  

```json
{
  "hits": {
    "total": 4,
    "hits": [
      {
        "_id": "2",
        "_source": {
          "name": "Alice",
          "age": 25,
          "gender": "F",
          "join_field": {
            "name": "employee",
            "parent": "1"
          }
        }
      },
      {
        "_id": "3",
        "_source": {
          "name": "Bob",
          "age": 28,
          "gender": "M",
          "join_field": {
            "name": "employee",
            "parent": "1"
          }
        }
      }
    ]
  }
}
```

💡 Agar tum **parent ID = 2** doge, to **department 2 ke employees** return honge!  

---

## 4️⃣ **Routing Ka Role** 📌  <a id="4"></a>

🚨 **Routing Elasticsearch ke shards manage karne me help karta hai.**  

Jab tum **parent_id query** run karte ho, Elasticsearch **automatically parent document ke ID ko routing parameter ke tarah use karta hai.**  

```json
GET my_index/_search?routing=1
{
  "query": {
    "parent_id": {
      "type": "employee",
      "id": "1"
    }
  }
}
```

🔹 Agar tum custom routing use kar rahe ho, to **`routing` parameter** explicitly specify karna padega.  

---

## 5️⃣ **Parent ID Query vs Has Parent Query** ⚖️  <a id="5"></a>

| Feature | `parent_id` Query | `has_parent` Query |
|---------|----------------|----------------|
| 🔍 **Purpose** | Parent ID ke basis pe **child documents** fetch karna | Child documents ke basis pe **parent documents** fetch karna |
| 🏗️ **Use Case** | Kis department ke under kaunse employees hain? | Kis employee ka department kya hai? |
| 🔄 **Performance** | Fast, kyunki direct **ID lookup** hota hai | Slow, kyunki **join operation** hota hai |

---

## 6️⃣ **Real-World Use Cases** 🌍  <a id="6"></a>

💼 **HR Database:** **Ek department me kaunse employees hain?**  
🛒 **E-Commerce:** **Ek category me kaunse products hain?**  
🏥 **Healthcare:** **Ek hospital me kaunse doctors kaam karte hain?**  

---

## 7️⃣ **Key Takeaways** 🎯  <a id="7"></a>

✅ **Parent ID Query** ka use karke tum **parent document ke ID** ke basis pe **child documents fetch** kar sakte ho.  
✅ **`type` option** me **child relation ka naam** dena zaroori hai.  
✅ Elasticsearch **automatically routing** handle karta hai, but **custom routing** ho to specify karna padta hai.  
✅ **Parent-Child relation me performance optimize karne ke liye routing ka role important hai.**  

---

