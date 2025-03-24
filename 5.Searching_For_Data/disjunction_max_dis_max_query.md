### 🚀 Disjunction Max (dis_max) Query

Elasticsearch ki **dis_max query** ek powerful compound query hai jo multiple query clauses ko execute karti hai, par **best-matching clause ka score use karti hai** document ke relevance score ke liye. Isme ek additional **tie breaker** parameter bhi hota hai jo documents ko reward karta hai agar woh multiple clauses match karte hain.

## 📑 **Table of Contents**  
1️⃣ **[Introduction](#1)** 🧐  
2️⃣ **[Basic Working of dis_max Query](#2)** ⚙️  
3️⃣ **[Example: Simple dis_max Query](#3)** 📌  
4️⃣ **[Tie Breaker: Rewarding Multi-Matching Documents](#4)** 🎯  
5️⃣ **[Comparison with multi_match Query](#5)** 🔄  
6️⃣ **[Performance Considerations](#6)** ⚡  
7️⃣ **[Conclusion](#7)** ✅  

---

## 🧐 **1. Introduction**  <a id="1"></a>  

Elasticsearch me agar hume **ek document ko multiple fields me search karna ho** aur best-matching field ka **highest relevance score lena ho**, tab **dis_max query** use hoti hai. Yeh bool query se thodi alag hai, kyunki yeh **best-matching score retain karti hai, na ki sabke scores ka sum leti hai**.

> **Bool Query**: Multiple clauses ke scores ka sum ya weighted sum leti hai.  
> **dis_max Query**: Sirf **sabse zyada relevant clause ka score** leti hai.  

---

## ⚙️ **2. Basic Working of dis_max Query**  <a id="2"></a>  

Disjunction Max Query ke andar multiple **query clauses** ho sakte hain. Agar **koi ek bhi clause match karta hai, to document match ho jata hai**. Relevance score **sirf sabse best-matching clause ka score** hota hai.

### 🔹 **Query Structure**  
```json
{
  "dis_max": {
    "queries": [
      { "match": { "name": "vegetable" } },
      { "match": { "tags": "vegetable" } }
    ]
  }
}
```
### 🔹 **Kaise Kaam Karti Hai?**
- **Agar kisi document me "vegetable" sirf name field me hai**, to uska score **match(name:vegetable)** ka score hoga.  
- **Agar kisi document me "vegetable" sirf tags field me hai**, to uska score **match(tags:vegetable)** ka score hoga.  
- **Agar kisi document me dono me hai, to sabse zyada relevance score wala match select hoga**.

---

## 📌 **3. Example: Simple dis_max Query**  <a id="3"></a>  

Maan lo, humare paas ek **products index** hai jisme documents kuch is tarah hain:

```json
[
  { "id": 1, "name": "Organic Vegetable Soup", "tags": ["healthy", "soup"] },
  { "id": 2, "name": "Fresh Green Vegetables", "tags": ["vegetable", "organic"] },
  { "id": 3, "name": "Tomato Ketchup", "tags": ["sauce", "tomato"] }
]
```
Aur agar hum yeh query run karein:
```json
{
  "dis_max": {
    "queries": [
      { "match": { "name": "vegetable" } },
      { "match": { "tags": "vegetable" } }
    ]
  }
}
```
### 🏆 **Expected Output**
```json
[
  { "_id": "2", "_score": 5.62, "name": "Fresh Green Vegetables" },
  { "_id": "1", "_score": 4.98, "name": "Organic Vegetable Soup" }
]
```
Yaha **document 2 ka score zyada hai** kyunki "vegetable" **tags field me match hua**, jo is case me zyada relevant hai.

---

## 🎯 **4. Tie Breaker: Rewarding Multi-Matching Documents**  <a id="4"></a>  

Agar ek document **multiple query clauses match kare**, to uska relevance score badhane ke liye **tie_breaker** parameter use hota hai. 

```json
{
  "dis_max": {
    "queries": [
      { "match": { "name": "vegetable" } },
      { "match": { "tags": "vegetable" } }
    ],
    "tie_breaker": 0.3
  }
}
```
### 🔹 **Kaise Kaam Karta Hai?**
- **Sabse highest relevance score select hota hai.**  
- **Agar koi aur clause bhi match karta hai, to uska score 0.3 se multiply hokar add hota hai.**

#### 🔢 **Calculation Example**  
Maan lo **document 2** ke relevance scores yeh hain:
- **match(name:vegetable)** → **4.2**
- **match(tags:vegetable)** → **5.62** (highest score)

Final Score Calculation:
```
5.62 + (4.2 * 0.3) = 6.88
```
### 🎯 **Final Output**
```json
[
  { "_id": "2", "_score": 6.88, "name": "Fresh Green Vegetables" },
  { "_id": "1", "_score": 5.48, "name": "Organic Vegetable Soup" }
]
```
Isme **document 2 ka score increase ho gaya** kyunki **usne dono clauses match kiye**. ⚡

---

## 🔄 **5. Comparison with multi_match Query**  <a id="5"></a>  

Dis_max query aur **multi_match query** dono kaafi similar lag sakti hain, par kaafi differences hain:

| Feature | dis_max Query | multi_match Query |
|---------|-------------|----------------|
| Query Type | Compound Query | Convenience Query |
| Score Calculation | Best relevance score select hota hai | Scores combine ho sakte hain |
| Tie Breaker Support | ✅ | ✅ |
| Flexibility | High (Compound queries support) | Moderate (Depends on match type) |
| Use Case | Jab best-matching field ka score chahiye | Jab multiple fields me search karna ho |

Multi-match query internally **dis_max query ka use karti hai** agar type **best_fields** ho.

```json
{
  "multi_match": {
    "query": "vegetable",
    "fields": ["name", "tags"],
    "type": "best_fields",
    "tie_breaker": 0.3
  }
}
```
Yeh query internally is dis_max query me convert hoti hai:
```json
{
  "dis_max": {
    "queries": [
      { "match": { "name": "vegetable" } },
      { "match": { "tags": "vegetable" } }
    ],
    "tie_breaker": 0.3
  }
}
```
To agar **multi_match ka best_fields type use kar rahe ho, to internally dis_max hi use ho raha hota hai**! 🔥

---

## ⚡ **6. Performance Considerations**  <a id="6"></a>  

- **dis_max query fast hoti hai** kyunki yeh sirf **highest score select karti hai** na ki saare scores ko sum karti hai.  
- **Tie breaker ka use optimization ke liye carefully karna chahiye** kyunki yeh extra calculations introduce karta hai.  
- **Agar aapko combined score chahiye to bool query ka use karein**, nahi to **dis_max best approach hai**.

---

## ✅ **7. Conclusion**  <a id="7"></a>  

✅ **dis_max query best relevance score select karti hai** from multiple query clauses.  
✅ **Tie breaker parameter multi-matching documents ko reward karta hai**.  
✅ **multi_match query ka best_fields type internally dis_max query hi hota hai**.  
✅ **Bool query aur dis_max me difference hai - bool query scores combine karti hai, dis_max sirf best score leti hai**.  

---

