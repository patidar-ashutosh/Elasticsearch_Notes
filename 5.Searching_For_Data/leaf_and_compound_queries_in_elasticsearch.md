# 📌 **Leaf and Compound Queries in Elasticsearch**  

Elasticsearch me queries **2 types** ki hoti hain:  
1. **Leaf Queries** 🍃 (Jo ek ya ek se zyada fields ke andar values search karti hain)  
2. **Compound Queries** 🔗 (Jo multiple queries ko logically combine karti hain)  

Agar tum **sirf ek condition** se search kar rahe ho, to leaf query kaafi hai.  
Agar **multiple conditions** combine karni hain (AND, OR, NOT, FILTER), to compound queries use karni padengi.  

# 📖 **Table of Contents**  
1️⃣ [**Introduction to Leaf Queries** 🍃](#1)  
2️⃣ [**Examples of Leaf Queries** 🛠️](#2)  
3️⃣ [**Introduction to Compound Queries** 🔗](#3)  
4️⃣ [**Types of Compound Queries** 🏗️](#4)  
5️⃣ [**Example: Searching for Alcohol Products** 🍷](#5)  
6️⃣ [**Nesting Compound Queries** 🎭](#6)  
7️⃣ [**Expected Output of Queries** 📜](#7)  
8️⃣ [**Conclusion** 🎯](#8)  

---

## 1️⃣ **Introduction to Leaf Queries** 🍃  <a id="1"></a>

Leaf queries **directly match** documents ke fields ke against. Yeh **independently work kar sakti hain** aur kisi aur query ko wrap karne ki zarurat nahi hoti.  

✅ **Examples of Leaf Queries:**  
- **Term Query**  
- **Match Query**  
- **Range Query**  
- **Exists Query**  

➡️ **Example:** Agar mujhe sirf vo documents chahiye jisme `"Alcohol"` tag ho, to main **leaf query** use karunga:  

```json
GET products/_search
{
  "query": {
    "term": {
      "tags": "Alcohol"
    }
  }
}
```

---

## 2️⃣ **Examples of Leaf Queries** 🛠️  <a id="2"></a>

| Query Type  | Description | Example |
|------------|-------------|---------|
| **Term Query** 🏷️ | Exact match karta hai kisi field ke value se | `"tags": "Alcohol"` |
| **Match Query** 🔍 | Full-text search karta hai aur relevance score calculate karta hai | `"description": "red wine alcohol"` |
| **Range Query** 📊 | Values ke range me documents filter karta hai | `"stock": {"lt": 10}` |
| **Exists Query** ✅ | Check karta hai ki koi field exist karti hai ya nahi | `"field": "discount"` |

---

## 3️⃣ **Introduction to Compound Queries** 🔗  <a id="3"></a>

Compound queries **multiple leaf queries ko combine** karne ke kaam aati hain.  

✅ **Compound queries ke bina hum ek hi time me sirf ek condition laga sakte hain**  
✅ **Compound queries ka use karne se hum multiple conditions ko combine kar sakte hain**  

➡️ **Example:** Agar mujhe `"Alcohol"` wale products search karne hain **jo sold out hone wale hain**, to mujhe **Term Query + Range Query** combine karna padega.  

```json
GET products/_search
{
  "query": {
    "bool": {
      "must": [
        { "term": { "tags": "Alcohol" }},
        { "range": { "stock": { "lt": 10 }}}
      ]
    }
  }
}
```

Yeh `"bool"` query ek **compound query** hai jo **Term Query + Range Query** ko logically AND me combine kar rahi hai.

---

## 4️⃣ **Types of Compound Queries** 🏗️  <a id="4"></a>

| Compound Query | Purpose | Example |
|--------------|---------|---------|
| **Bool Query** 🤖 | Multiple queries ko AND, OR, NOT ke logic ke sath combine karta hai | `must`, `should`, `filter`, `must_not` |
| **Dis Max Query** 🎯 | Multiple queries run karta hai aur best matching result return karta hai | `"queries": [{}, {}]` |
| **Function Score Query** 🔢 | Documents ka score modify karta hai based on custom functions | `"functions": [{ "field_value_factor": {}}]` |

---

## 5️⃣ **Example: Searching for Alcohol Products** 🍷  <a id="5"></a>

Mujhe **"Alcohol"** wale products dhoondhne hain jo:  
- Sold out ho chuke hain **(stock = 0)**  
- Ya inactive ho chuke hain **(status = inactive)**  

### **SQL Equivalent Query**  
```sql
SELECT * FROM products
WHERE tags = 'Alcohol' 
AND (stock = 0 OR status = 'inactive');
```

### **Elasticsearch Query**  
```json
GET products/_search
{
  "query": {
    "bool": {
      "must": [
        { "term": { "tags": "Alcohol" }}
      ],
      "should": [
        { "term": { "stock": 0 }},
        { "term": { "status": "inactive" }}
      ],
      "minimum_should_match": 1
    }
  }
}
```

✅ **Explanation:**  
- **must** ➝ `"Alcohol"` tag hona chahiye  
- **should** ➝ `"stock": 0"` **ya** `"status": "inactive"` hona chahiye  
- **minimum_should_match: 1** ➝ At least **ek should condition satisfy** honi chahiye  

---

## 6️⃣ **Nesting Compound Queries** 🎭  <a id="6"></a>

Agar aur complex queries likhni hain, to **compound queries ke andar bhi compound queries** nest kar sakte hain.  

➡️ **Example:** Agar mujhe `"Alcohol"` products dhoondhne hain jo ya to `"inactive"` ho ya `"stock < 10"` ho, aur **unpar discount bhi apply ho** raha ho, to query kuch aisi hogi:  

```json
GET products/_search
{
  "query": {
    "bool": {
      "must": [
        { "term": { "tags": "Alcohol" }},
        {
          "bool": {
            "should": [
              { "term": { "status": "inactive" }},
              { "range": { "stock": { "lt": 10 }}}
            ],
            "minimum_should_match": 1
          }
        }
      ],
      "filter": [
        { "exists": { "field": "discount" }}
      ]
    }
  }
}
```

✅ **Explanation:**  
- `"must"` ➝ `"Alcohol"` tag hona chahiye  
- `"should"` ➝ `"inactive"` **ya** `"stock < 10"`  
- `"filter"` ➝ `"discount"` field exist karni chahiye  

---

## 7️⃣ **Expected Output of Queries** 📜  <a id="7"></a>

Agar documents kuch is type ke hain:  

```json
[
  { "id": 1, "tags": ["Alcohol"], "stock": 5, "status": "active", "discount": 10 },
  { "id": 2, "tags": ["Alcohol"], "stock": 0, "status": "inactive", "discount": 5 },
  { "id": 3, "tags": ["Non-Alcohol"], "stock": 3, "status": "active", "discount": 15 }
]
```

**Query ka output:**  
```json
[
  { "id": 2, "tags": ["Alcohol"], "stock": 0, "status": "inactive", "discount": 5 }
]
```
Sirf wahi document aayega **jo conditions satisfy karta hai**.

---

## 8️⃣ **Conclusion** 🎯  <a id="8"></a>

✅ **Leaf Queries** sirf ek field pe kaam karti hain (e.g. `term`, `match`, `range`).  
✅ **Compound Queries** multiple queries ko logically combine karti hain (`bool`, `dis_max`, `function_score`).  
✅ **Bool Queries me nesting** karke hum complex conditions likh sakte hain.  
✅ **Elasticsearch me SQL jaisi queries likhne ke liye Compound Queries ka use hota hai.**  

