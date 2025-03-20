## 📜 **Table of Contents**  

1️⃣ **[Has Parent Query Kya Hai?](#1-has-parent-query-kya-hai) 🔍**  
2️⃣ **[Basic Query Syntax](#2-basic-query-syntax) 📝**  
3️⃣ **[Query Ka Expected Output](#3-query-ka-expected-output) 📊**  
4️⃣ **[Relevance Score Ka Role](#4-relevance-score-ka-role) 📌**  
5️⃣ **[Parent ID Query vs Has Parent Query](#5-parent-id-query-vs-parent-id-query) ⚖️**  
6️⃣ **[Real-World Use Cases](#6-real-world-use-cases) 🌍**  

---

## 1️⃣ **Has Parent Query Kya Hai?** 🔍  

Agar tumhe kisi **parent document ke kuch conditions** ke basis pe **child documents** retrieve karne hain, to `has_parent` query use hoti hai.  

💡 Example:  
- **Department (Parent)** → **Employees (Children)**  
- Tum **"Development" department ke saare employees** dekhna chahte ho, **bina department ID jane**, tab **has_parent query** ka use karoge.  

---

## 2️⃣ **Basic Query Syntax** 📝  

Chalo ek query likhte hain jo **Development department ke saare employees** ko return karegi:  

```json
GET my_index/_search
{
  "query": {
    "has_parent": {
      "parent_type": "department",
      "query": {
        "term": {
          "name.keyword": "Development"
        }
      }
    }
  }
}
```

🔹 **`parent_type: "department"`** → Ye batata hai ki parent document **department** hai.  
🔹 **`query` ke andar `term` query** → Ye **Development department** ke parent documents dhundta hai.  
🔹 **Jo employees in parent documents ke andar honge, wo return honge!**  

---

## 3️⃣ **Query Ka Expected Output** 📊  

Agar **Development department (ID = 1)** ke andar **4 employees** hain, to response kuch aisa aayega:  

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
          "gender": "F"
        }
      },
      {
        "_id": "3",
        "_source": {
          "name": "Bob",
          "age": 28,
          "gender": "M"
        }
      }
    ]
  }
}
```

💡 **Matlab bina parent ID jane bhi tum easily child documents fetch kar sakte ho!**  

---

## 4️⃣ **Relevance Score Ka Role** 📌  

🚨 **Default behavior:** Has Parent Query **parent document ka relevance score ignore karti hai**, aur **flat score = 1** assign hota hai.  

🔹 Agar tum **score option ko true set kar do**, to **parent document ka relevance score child documents par apply ho jata hai**:  

```json
GET my_index/_search
{
  "query": {
    "has_parent": {
      "parent_type": "department",
      "query": {
        "match": {
          "description": "software development"
        }
      },
      "score": true
    }
  }
}
```

🔹 **Score = true** karne se **jo parent documents best match karenge**, unke child documents higher rank pe aayenge.  

💡 **Jab tum text-based search kar rahe ho, tab yeh scoring useful hoti hai!**  

---

## 5️⃣ **Parent ID Query vs Has Parent Query** ⚖️  

| Feature | `parent_id` Query | `has_parent` Query |
|---------|----------------|----------------|
| 🔍 **Purpose** | Parent ID ke basis pe **child documents** fetch karna | Parent document ke **conditions** ke basis pe **child documents** fetch karna |
| 🏗️ **Use Case** | Kis department ke under kaunse employees hain? | Kaunse employees kisi **specific condition wale department** me hain? |
| 🔄 **Flexibility** | Parent ID ka pata hona chahiye | Parent ka **koi bhi field match** kar sakta hai |
| 📊 **Relevance Scoring** | Nahi hota | Ho sakta hai (`score: true`) |

---

## 6️⃣ **Real-World Use Cases** 🌍  

💼 **HR Database:** **Kaunse employees kisi specific department me hain?**  
🛒 **E-Commerce:** **Kaunse products kisi discount category ke andar aate hain?**  
🏥 **Healthcare:** **Kaunse patients kisi specific hospital branch me admitted hain?**  

---

## 🎯 **Key Takeaways**  

✅ **Has Parent Query** ka use karke tum **parent document ke conditions** ke basis pe **child documents fetch** kar sakte ho.  
✅ **`parent_type` option** me **parent relation ka naam** dena zaroori hai.  
✅ **Scoring enable karke tum relevance improve kar sakte ho!**  
✅ **Parent ID query se zyada flexible hai, jab tumhe parent ki ID na pata ho!**  

---

