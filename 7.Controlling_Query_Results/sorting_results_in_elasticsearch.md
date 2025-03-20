# 📌 Sorting Results in **Elasticsearch**  

## 📑 Table of Contents  
1. 🔍 **Introduction** – Sorting Basics  
2. 📜 **Simple Sorting** – Sorting by a Single Field  
3. 🔄 **Sorting Order** – Ascending vs. Descending  
4. 🔢 **Sorting by Multiple Fields**  
5. 🎯 **Sorting by Score**  
6. 🛠 **Elasticsearch’s Internal Sorting Mechanism**  
7. ✅ **Conclusion & Key Takeaways**  

---

## 🔍 **1. Introduction** – Sorting Basics  
Sorting results in **Elasticsearch** is similar to using `ORDER BY` in SQL. Agar tumhare paas kaafi saare **documents** hain aur tum chahte ho ki **koi specific order me aaye**, toh tum **sort** parameter ka use kar sakte ho.  

Jaise agar tumhe **sabse kam preparation time wale recipes dikhani hain**, toh tum **preparation_time_minutes** field pe **ascending order me sort** kar sakte ho.

---

## 📜 **2. Simple Sorting** – Sorting by a Single Field  

### ✅ **Query:** Sorting Recipes by Preparation Time (Ascending)
```json
{
  "query": {
    "match_all": {}
  },
  "sort": ["preparation_time_minutes"]
}
```
### 🔍 **Output:**
- Recipes sabse kam **preparation_time_minutes** wale **pehle** dikhengi.
- Default **ascending (ASC)** order hota hai.

#### 📌 **Alternative (Explicit ASC Order)**
```json
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "preparation_time_minutes": "asc"
    }
  ]
}
```
Same result aayega, lekin explicitly `asc` likhna readability ke liye better hai.

---

## 🔄 **3. Sorting Order** – Ascending vs. Descending  
Sorting order ko **asc (ascending)** ya **desc (descending)** se define kar sakte ho.

### ✅ **Query:** Sorting by `created` Field (Descending)
```json
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "created": "desc"
    }
  ],
  "_source": ["created"]
}
```
### 🔍 **Output:**
- **Sabse naye** (`latest`) documents **pehle** dikhte hain.
- `created` field `_source` me include kiya hai taaki human-readable date dikhe.

📌 **Note:**  
- Elasticsearch internally **dates ko milliseconds since epoch** me store karta hai (Unix timestamp × 1000).
- Sorting internally **numeric representation** pe hoti hai.

---

## 🔢 **4. Sorting by Multiple Fields**  
Kabhi-kabhi tumhe ek se **zyada fields** ke basis pe sorting karni pad sakti hai.  

### ✅ **Query:**  
1. **Preparation Time (Ascending)**
2. **Created Timestamp (Descending)**
```json
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "preparation_time_minutes": "asc"
    },
    {
      "created": "desc"
    }
  ],
  "_source": ["preparation_time_minutes", "created"]
}
```
### 🔍 **Output:**
- **Pehle preparation time kam wale recipes aayengi**.
- **Agar preparation time same hai, toh naye (`latest`) pehle aayenge.**

📌 **Sorting ka effect sirf un documents pe dikhega jo pehle wale field ke basis pe equal hain.**  
**Example:**  
```
Preparation Time | Created Date
----------------|-------------
15 min         | 2025-03-01
15 min         | 2025-03-03
15 min         | 2025-03-05  ✅ (sabse pehle)
```

---

## 🎯 **5. Sorting by Score**  
Elasticsearch me by default results **relevance score ke basis pe sort hote hain**, par agar tum **custom sorting** use kar rahe ho toh manually `_score` include karna padega.

### ✅ **Query:** Sorting by Score
```json
{
  "query": {
    "match": {
      "title": "pasta"
    }
  },
  "sort": ["_score"]
}
```
### 🔍 **Output:**
- **Sabse relevant** results pehle aayenge (default behavior).

📌 **Sorting by `_score` useful hai jab tumne manually sorting define ki ho par relevance maintain rakhna chahte ho.**

---

## 🛠 **6. Elasticsearch’s Internal Sorting Mechanism**  
1. **Dates →** Elasticsearch internally dates ko **milliseconds since epoch** me store karta hai.
2. **Text Fields →** Directly sort nahi ho sakti (`keyword` type ya `fielddata: true` lagana padta hai).
3. **Score Sorting →** Default sorting `_score` ke basis pe hoti hai.

📌 **Sorting best practices:**  
- Strings ko sort karne ke liye **`keyword`** type ka field use karo.
- `fielddata: true` use karna **memory-intensive** ho sakta hai.

---

## ✅ **7. Conclusion & Key Takeaways**  
✔ **Sorting Elasticsearch me simple hai, SQL ke ORDER BY jaisa kaam karta hai.**  
✔ **Default sorting ascending hoti hai, explicitly define bhi kar sakte ho.**  
✔ **Multiple fields pe sort karna possible hai (pehle primary sort, phir secondary sort).**  
✔ **Date fields internally milliseconds me store hote hain (epoch time × 1000).**  
✔ **_score field sorting default hoti hai, lekin manually bhi define kar sakte ho.**  

---

