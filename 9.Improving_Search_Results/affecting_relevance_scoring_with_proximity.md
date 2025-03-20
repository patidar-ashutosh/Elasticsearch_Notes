## 🔍 Affecting Relevance Scoring with Proximity  

### 📌 **Table of Contents**  
1️⃣ [Introduction: Relevance Scoring & Proximity](#introduction)  
2️⃣ [Match vs. Match Phrase Query](#match-vs-match-phrase-query)  
3️⃣ [Boolean Query for Best of Both Worlds](#boolean-query)  
4️⃣ [Boosting Relevance with Proximity](#boosting-relevance)  
5️⃣ [Using Slop Parameter for Flexibility](#slop-parameter)  
6️⃣ [Key Takeaways](#key-takeaways)  

---

## 📖 **1. Introduction: Relevance Scoring & Proximity** <a name="introduction"></a>  
Jab **Elasticsearch** documents ko match karta hai, to sirf **terms ka presence** nahi, balki **unki proximity (nazdeeki)** bhi relevance score ko affect karti hai.  
**Jitne words ek dusre ke kareeb honge, utni zyada relevance hogi.**  

🚀 **Example**  
Agar hum **"spicy sauce"** search karein aur ek document me  
1️⃣ `"I love spicy sauce"`  
2️⃣ `"This sauce is spicy"`  
ho, to **pehla document zyada relevant hoga** kyunki **spicy aur sauce ek saath hai.**  

---

## 🆚 **2. Match vs. Match Phrase Query** <a name="match-vs-match-phrase-query"></a>  

🔹 **Match Query:** Words ki **presence** matter karti hai, order aur proximity nahi.  
🔹 **Match Phrase Query:** Words **specified order me hone chahiye** aur proximity matter karti hai.  

💡 **Example**  
Agar query `"spicy sauce"` hai:  
| Query Type        | `"I love spicy sauce"` | `"This sauce is spicy"` |  
|------------------|----------------------|----------------------|  
| **Match Query**   | ✅ Match             | ✅ Match             |  
| **Match Phrase**  | ✅ Match             | ❌ No Match          |  

👉 **Match Phrase Query tabhi match karega jab words ek order me ho!**  

---

## 🔗 **3. Boolean Query for Best of Both Worlds** <a name="boolean-query"></a>  

Agar **match query** se documents mil rahe hain **(chahe terms kisi bhi order me ho)**  
aur **match phrase query** se **proximity boost** mil raha hai,  
to dono ko **bool query** se combine kar sakte hain!  

📌 **Syntax:**  
```json
{
  "query": {
    "bool": {
      "must": [ 
        { "match": { "title": "spicy sauce" } } 
      ],
      "should": [
        { "match_phrase": { "title": "spicy sauce" } }
      ]
    }
  }
}
```

✅ **Yeh query ensure karegi ki:**  
1️⃣ Documents match hone chahiye **(must condition)**  
2️⃣ Jisme words kareeb ho, unka **score boost ho** *(should condition)*  

📊 **Expected Output**  
- `"I love spicy sauce"` → **High Score** ✅  
- `"This sauce is spicy"` → **Lower Score** ✅  

---

## 📈 **4. Boosting Relevance with Proximity** <a name="boosting-relevance"></a>  

**Bool query** me **match phrase query** ka use karke proximity se relevance boost kar sakte hain.  
✅ Jo words **ek saath honge, unka score zyada hoga!**  

---

## ⚙️ **5. Using Slop Parameter for Flexibility** <a name="slop-parameter"></a>  

🎯 **Problem:**  
Agar match phrase query strict hai, to `"I love spicy sauce"` match karega,  
lekin `"I love super spicy red sauce"` match nahi karega.  

💡 **Solution:**  
Slop parameter **kitne extra words allowed hain ye define karta hai.**  

📌 **Example Query:**  
```json
{
  "query": {
    "bool": {
      "must": [ 
        { "match": { "title": "spicy sauce" } } 
      ],
      "should": [
        { "match_phrase": { "title": { "query": "spicy sauce", "slop": 5 } } }
      ]
    }
  }
}
```
👆 **Slop = 5 ka matlab:** `"I love super spicy red sauce"` **bhi match hoga!**  

📊 **Expected Output:**  
| Document | Match Score |  
|----------|------------|  
| `"I love spicy sauce"` | **High** ✅ |  
| `"I love super spicy red sauce"` | **Medium** ✅ |  
| `"This sauce is spicy"` | **Low** ✅ |  

👉 **Jitna kam slop, utni strict match!**  

---

## 🏆 **6. Key Takeaways** <a name="key-takeaways"></a>  

✅ **Proximity matters:** Words jitne kareeb honge, utna zyada relevance score milega.  
✅ **Match vs. Match Phrase Query:**  
- Match query sirf terms ka hona check karta hai.  
- Match phrase query **order aur proximity bhi check karta hai.**  
✅ **Bool Query:** Dono ka **best combination** hai.  
✅ **Slop Parameter:** **Flexibility badhane ke liye use hota hai!**  

---

💡 **Conclusion:**  
Agar tumhe relevance improve karni ho, to **match aur match phrase queries ko combine karo** aur **slop parameter use karo**. Yeh trick tumhare **search results ko smart aur accurate banayegi!** 🚀