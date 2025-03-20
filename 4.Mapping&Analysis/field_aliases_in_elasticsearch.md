# **📌 Field Aliases in Elasticsearch**  

## **📜 Table of Contents**
1. [Field Aliases Kya Hota Hai?](#field-aliases-kya-hota-hai)  
2. [Field Aliases Kyon Zaroori Hai?](#field-aliases-kyon-zaroori-hai)  
3. [Field Alias Kaise Kaam Karta Hai?](#field-alias-kaise-kaam-karta-hai)  
4. [Field Alias Implement Kaise Karein?](#field-alias-implement-kaise-karein)  
5. [Field Alias Ko Query Mein Kaise Use Karein?](#field-alias-ko-query-mein-kaise-use-karein)  
6. [Field Alias Update Kaise Karein?](#field-alias-update-kaise-karein)  
7. [Field Aliases Ki Limitations](#field-aliases-ki-limitations)  
8. [Cluster Level Index Aliases](#cluster-level-index-aliases)  
9. [Conclusion](#conclusion)  

---

## **1️⃣ Field Aliases Kya Hota Hai?**  
Field Aliases Elasticsearch ka ek feature hai jo tumhe bina **reindexing kiye** kisi field ka **alternate naam** use karne ki suvidha deta hai. Matlab agar tum **"content"** field ka naam **"comment"** rakhna chahte ho bina naye index banaye, toh tum **alias** ka use kar sakte ho.  

🔹 **Example:**  
Tumhare paas ek index hai `blog_posts` jisme ek field hai `"content"`, agar tum ise `"comment"` naam se query karna chahte ho, toh alias use karke bina reindexing kiye yeh possible hai.  

---

## **2️⃣ Field Aliases Kyon Zaroori Hai?**  
Agar tumhare paas **millions of documents** hai aur tumhe kisi field ka naam change karna ho, toh normally tumhe **poore data ko reindex karna padega**, jo **expensive aur time-consuming** ho sakta hai. **Field aliases** iss problem ka solution dete hain kyunki:  

✅ **Reindexing ki zaroorat nahi hoti**  
✅ **Backward Compatibility maintain hoti hai**  
✅ **Search Queries ko simplify kar sakte ho**  

---

## **3️⃣ Field Alias Kaise Kaam Karta Hai?**  
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

## **4️⃣ Field Alias Implement Kaise Karein?**  
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

## **5️⃣ Field Alias Ko Query Mein Kaise Use Karein?**  
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

## **6️⃣ Field Alias Update Kaise Karein?**  
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

## **7️⃣ Field Aliases Ki Limitations**  
Field Aliases powerful hai, par kuch **limitations** bhi hai:  

❌ Alias ko **nested fields ke liye use nahi** kar sakte  
❌ Alias **multi-fields ko support nahi karta**  
❌ Alias ko **aggregations mein directly use nahi kar sakte**  

---

## **8️⃣ Cluster Level Index Aliases**  
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

## **9️⃣ Conclusion**  
**Field Aliases** kaafi useful hai jab tum **bina reindexing kiye kisi field ka naam change** karna chahte ho. Yeh **query parsing ke time alias ko actual field name se replace kar** deta hai.  

**🔥 Key Points:**  
✅ **Field Aliases reindexing ki zaroorat nahi padne dete**  
✅ **Query aur Index Requests dono me kaam karte hain**  
✅ **Sirf path update kiya ja sakta hai, alias ka naam nahi**  
✅ **Aggregations aur nested fields ke saath kaam nahi karta**  

---
[Reference Video Link](https://youtu.be/rnLNLUts0t4?si=_qnKpC2tCobE-VDb)
---