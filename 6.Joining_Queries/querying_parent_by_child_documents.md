ab tak humne **has_parent query** dekhi thi, jisme **parent ki conditions ke basis pe child documents retrieve** karte the. 🤔  

Ab **opposite case** dekhenge: **Parent documents ko retrieve karna based on child documents' conditions** using `has_child` query. 🚀  

## 📜 **Table of Contents**  

1️⃣ **[Has Child Query Kya Hai?](#1) 🔍**  
2️⃣ **[Basic Query Syntax](#2) 📝**  
3️⃣ **[Query Ka Expected Output](#3) 📊**  
4️⃣ **[Relevance Score Ka Role](#4) 🎯**  
5️⃣ **[Min & Max Children Filter](#5) 📌**  
6️⃣ **[Parent-Child Sorting](#6) 🔀**  
7️⃣ **[Real-World Use Cases](#7) 🌍**  
8️⃣ **[Key Takeaways](#8) 🎯**

---

## 1️⃣ **Has Child Query Kya Hai?** 🔍  <a id="1"></a>

`has_child` query ka kaam hai **parent documents ko retrieve karna** based on **conditions jo child documents par lagti hain**.  

💡 Example:  
- **Department (Parent)** → **Employees (Children)**  
- Tum **aise departments dekhna chahte ho jinme employees ki age 50+ ho** → `has_child` query use karoge.  

---

## 2️⃣ **Basic Query Syntax** 📝  <a id="2"></a>

Chalo ek query likhte hain jo **aise departments fetch karegi jinme 50+ age wale employees hon**:  

```json
GET my_index/_search
{
  "query": {
    "has_child": {
      "type": "employee",
      "query": {
        "range": {
          "age": {
            "gte": 50
          }
        }
      }
    }
  }
}
```

🔹 **`type: "employee"`** → Yeh batata hai ki child document ka type **employee** hai.  
🔹 **`query` ke andar `range` query** → Sirf **wo parents milenge jinme 50+ age wale employees hain**.  

---

## 3️⃣ **Query Ka Expected Output** 📊  <a id="3"></a>

Agar **Development department** me **52 age ka employee** hai, to response kuch aisa aayega:  

```json
{
  "hits": {
    "total": 1,
    "hits": [
      {
        "_id": "1",
        "_source": {
          "name": "Development",
          "location": "New York"
        }
      }
    ]
  }
}
```

💡 **Matlab sirf wo departments return honge jinme specified conditions wale employees hain!**  

---

## 4️⃣ **Relevance Score Ka Role** 🎯  <a id="4"></a>

🚨 **Default Behavior:** Parent documents ka relevance score **child documents ke score se affect nahi hota**.  

🔹 **Agar tum score mode enable karo**, to **child documents ka score aggregate hokar parent ke score me add ho jata hai**:  

```json
GET my_index/_search
{
  "query": {
    "has_child": {
      "type": "employee",
      "query": {
        "match": {
          "skills": "JavaScript"
        }
      },
      "score_mode": "sum"
    }
  }
}
```

| **Score Mode** | **Behavior** |
|---------------|-------------|
| **min** | Sabse low score child ka score parent ka score banega |
| **max** | Sabse high score child ka score parent ka score banega |
| **sum** | Saare matching child documents ke scores ko **add** kar dega |
| **avg** | Saare matching child documents ke scores ka **average** le lega |
| **none** | Default mode → **Child ka score affect nahi karega** |

💡 **Jab tum scoring-based ranking chahte ho, tab `score_mode` useful hota hai!**  

---

## 5️⃣ **Min & Max Children Filter** 📌  <a id="5"></a>

Kabhi kabhi tumhe yeh bhi define karna padta hai ki **ek parent ke andar minimum aur maximum kitne matching child documents hone chahiye**.  

```json
GET my_index/_search
{
  "query": {
    "has_child": {
      "type": "employee",
      "query": {
        "range": {
          "age": {
            "gte": 50
          }
        }
      },
      "min_children": 2,
      "max_children": 5
    }
  }
}
```

🔹 **`min_children: 2`** → Sirf wo parents match honge jinme **kam se kam 2 matching employees** hain.  
🔹 **`max_children: 5`** → Sirf wo parents match honge jinme **zyada se zyada 5 matching employees** hain.  

💡 **Agar ek department me sirf ek hi employee 50+ age ka hoga, to wo result me nahi aayega!**  

---

## 6️⃣ **Parent-Child Sorting** 🔀  <a id="6"></a>

`has_child` query me **parent documents ko child documents ke basis pe sort bhi kar sakte ho**, lekin iske liye **scripting required hoti hai**.  

Jab tumhe **kisi parent document ko uske child documents ki kisi field ke basis pe sort karna ho**, tab **script-based sorting** use hoti hai.  

🚨 **Ye advanced feature hai, aur mostly Elasticsearch scripting (Painless) ka knowledge chahiye hota hai.**  

Agar chahiye to batao, ek example de sakta hoon! 😎  

---

## 7️⃣ **Real-World Use Cases** 🌍  <a id="7"></a>

📊 **HR Database:** **Wo departments dikhana jinme 10+ employees ho**  
🛒 **E-Commerce:** **Aise sellers fetch karna jinme 4-star se kam rating wale products ho**  
🏥 **Healthcare:** **Wo hospitals dikhana jinme 5+ COVID patients ho**  

---

## 8️⃣ **Key Takeaways** 🎯  <a id="8"></a>

✅ **Has Child Query** ka use karke tum **child documents ke conditions ke basis pe parent documents retrieve kar sakte ho.**  
✅ **`type` option** me **child document ka type define karna zaroori hai.**  
✅ **Scoring modes enable karke tum relevance ranking improve kar sakte ho!**  
✅ **`min_children` aur `max_children` options se tum filtering aur better control kar sakte ho.**  

---

