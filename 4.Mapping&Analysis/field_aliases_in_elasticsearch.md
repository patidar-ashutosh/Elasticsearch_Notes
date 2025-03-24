# **📌 Field Aliases in Elasticsearch**  

## **📜 Table of Contents**  
1️⃣ [Field Aliases Kya Hota Hai?](#1)  
2️⃣ [Field Aliases Kyon Zaroori Hai?](#2)  
3️⃣ [Field Alias Kaise Kaam Karta Hai?](#3)  
4️⃣ [Field Alias Implement Kaise Karein?](#4)  
5️⃣ [Field Alias Ko Query Mein Kaise Use Karein?](#5)  
6️⃣ [Field Alias Update Kaise Karein?](#6)  
7️⃣ [Field Aliases Ki Limitations](#7)  
8️⃣ [Cluster Level Index Aliases](#8)  
9️⃣ [Conclusion](#9)  

---

## **1️⃣ Field Aliases Kya Hota Hai?**  <a id="1"></a>
Field Aliases Elasticsearch ka ek feature hai jo tumhe bina **reindexing kiye** kisi field ka **alternate naam** use karne ki suvidha deta hai. Matlab agar tum **"content"** field ka naam **"comment"** rakhna chahte ho bina naye index banaye, toh tum **alias** ka use kar sakte ho.  

🔹 **Example:**  
Tumhare paas ek index hai `blog_posts` jisme ek field hai `"content"`, agar tum ise `"comment"` naam se query karna chahte ho, toh alias use karke bina reindexing kiye yeh possible hai.  

---

## **2️⃣ Field Aliases Kyon Zaroori Hai?**  <a id="2"></a>
Agar tumhare paas **millions of documents** hai aur tumhe kisi field ka naam change karna ho, toh normally tumhe **poore data ko reindex karna padega**, jo **expensive aur time-consuming** ho sakta hai. **Field aliases** iss problem ka solution dete hain kyunki:  

✅ **Reindexing ki zaroorat nahi hoti**  
✅ **Backward Compatibility maintain hoti hai**  
✅ **Search Queries ko simplify kar sakte ho**  

---

## **3️⃣ Field Alias Kaise Kaam Karta Hai?**  <a id="3"></a>
Field Alias ek **mapping level construct** hota hai jo ek field ko **alternate naam se refer karne** ki suvidha deta hai.  

🔹 Jab tum ek **alias define** karte ho, toh woh **sirf query parsing** ke time kaam karta hai.  
🔹 Actual data store aur retrieve **original field** se hota hai, **alias sirf ek reference** hota hai.  

### **📌 Example Diagram**
```
+--------------+
|   "comment"  | ---> Alias for ---> | "content" |
+--------------+                     +-----------+
```

---

## **4️⃣ Field Alias Implement Kaise Karein?**  <a id="4"></a>
Agar tum **"content"** field ka alias **"comment"** banana chahte ho, toh tumhe yeh mapping update karni padegi:

### **📌 Mapping Create Karna**
```json
PUT blog_posts
{
  "mappings": {
    "properties": {
      "content": { "type": "text" },
      "comment": { 
        "type": "alias",
        "path": "content"
      }
    }
  }
}
```
🔹 **"comment"** field ab ek alias hai jo **"content"** field ko refer karega.  

---

## **5️⃣ Field Alias Ko Query Mein Kaise Use Karein?**  <a id="5"></a>
Ab tum **"comment"** field ka use query mein **"content"** field ki tarah kar sakte ho.

### **📌 Original Query (content field se)**
```json
GET blog_posts/_search
{
  "query": {
    "match": {
      "content": "Elasticsearch is powerful"
    }
  }
}
```

### **📌 Alias Query (comment field se)**
```json
GET blog_posts/_search
{
  "query": {
    "match": {
      "comment": "Elasticsearch is powerful"
    }
  }
}
```
🔹 **Dono queries same result dengi**, kyunki **"comment" alias** actually **"content"** field ka reference hai.  

---

## **6️⃣ Field Alias Update Kaise Karein?**  <a id="6"></a>
Agar tum **alias ka target field change karna chahte ho**, toh tum **mapping update** kar sakte ho.

### **📌 Alias Update Karna**
```json
PUT blog_posts/_mapping
{
  "properties": {
    "comment": {
      "type": "alias",
      "path": "new_content"
    }
  }
}
```
🔹 Ab `"comment"` alias `"new_content"` field ko point karega.  

⚠ **Note:** Tum **alias ka naam change nahi** kar sakte, sirf **path update** kar sakte ho.  

---

## **7️⃣ Field Aliases Ki Limitations**  <a id="7"></a>
Field Aliases powerful hai, par kuch **limitations** bhi hai:  

❌ Alias ko **nested fields ke liye use nahi** kar sakte  
❌ Alias **multi-fields ko support nahi karta**  
❌ Alias ko **aggregations mein directly use nahi kar sakte**  

---

## **8️⃣ Cluster Level Index Aliases**   <a id="8"></a>
Field aliases ke alawa Elasticsearch me **Index Aliases** bhi hote hain jo **Cluster Level pe apply** hote hain.  

### **📌 Example: Index Alias Create Karna**
```json
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "blog_posts_v1",
        "alias": "blog_posts"
      }
    }
  ]
}
```
🔹 **"blog_posts"** alias **"blog_posts_v1"** index ko refer karega.  

📌 **Benefit:** Tum bina naye index create kiye, **index switching** kar sakte ho.  

---

## **9️⃣ Conclusion**  <a id="9"></a>
**Field Aliases** kaafi useful hai jab tum **bina reindexing kiye kisi field ka naam change** karna chahte ho. Yeh **query parsing ke time alias ko actual field name se replace kar** deta hai.  

**🔥 Key Points:**  
✅ **Field Aliases reindexing ki zaroorat nahi padne dete**  
✅ **Query aur Index Requests dono me kaam karte hain**  
✅ **Sirf path update kiya ja sakta hai, alias ka naam nahi**  
✅ **Aggregations aur nested fields ke saath kaam nahi karta**  

---
[Reference Video Link](https://youtu.be/rnLNLUts0t4?si=_qnKpC2tCobE-VDb)
---