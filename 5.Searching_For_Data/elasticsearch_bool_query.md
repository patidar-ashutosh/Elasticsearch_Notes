# 📌 Elasticsearch `bool` Query 

## 📜 Table of Contents  

1. 🔍 **Introduction to `bool` Query**  
2. 🏗 **Structure of `bool` Query**  
3. 🎭 **Clauses of `bool` Query**  
   - ✅ `must`
   - 🏆 `filter`
   - 🔄 `should`
   - 🚫 `must_not`
4. ⚖ **Difference Between `must` & `filter`**  
5. 💡 **Use Cases of `bool` Query**  
6. 📝 **Examples with Expected Output**  
7. 🎯 **Performance Optimization Tips**  
8. 🔗 **Related Queries**  

---

## 🔍 1. Introduction to `bool` Query  

`bool` query ka use tab hota hai jab hume multiple conditions ko ek saath combine karna hota hai. Ye ek **compound query** hai jo alag-alag clauses ko combine karke ek complex query banane ka kaam karti hai.  

Jab hume multiple conditions apply karni ho, jaise ki:  
- **Agar ek document specific field match kare (`must`)**
- **Agar ek document specific filter satisfy kare (`filter`)**
- **Agar ek document optional condition match kare (`should`)**
- **Agar ek document kisi condition ko match na kare (`must_not`)**  

Toh `bool` query best solution hai!  

---

## 🏗 2. Structure of `bool` Query  

Elasticsearch me `bool` query ka structure kuch is tarah hota hai:

```json
{
  "query": {
    "bool": {
      "must": [
        { "match": { "field1": "value1" } }
      ],
      "filter": [
        { "term": { "field2": "value2" } }
      ],
      "should": [
        { "match": { "field3": "value3" } }
      ],
      "must_not": [
        { "match": { "field4": "value4" } }
      ]
    }
  }
}
```

**Iska Matlab:**  
- **`must`** → Yeh condition match honi chahiye  
- **`filter`** → Yeh condition bina scoring ke match honi chahiye  
- **`should`** → Yeh condition optional hai, lekin agar match ho toh score badh jayega  
- **`must_not`** → Yeh condition match nahi honi chahiye  

---

## 🎭 3. Clauses of `bool` Query  

### ✅ `must` Clause  

- Yeh condition **zaroori** hoti hai  
- Yeh documents ka score bhi influence karti hai  
- Example:

```json
{
  "query": {
    "bool": {
      "must": [
        { "match": { "title": "Elasticsearch" } }
      ]
    }
  }
}
```

📌 **Expected Output** – Sirf wahi documents aayenge jisme `"title": "Elasticsearch"` hoga aur unka score bhi calculate hoga.  

---

### 🏆 `filter` Clause  

- Yeh condition bhi zaroori hoti hai **lekin** scoring ko affect nahi karti  
- Performance-wise `must` se fast hoti hai  
- Example:

```json
{
  "query": {
    "bool": {
      "filter": [
        { "term": { "category": "database" } }
      ]
    }
  }
}
```

📌 **Expected Output** – Sirf wahi documents aayenge jisme `"category": "database"` hoga, **lekin yeh scoring affect nahi karega**.  

---

### 🔄 `should` Clause  

- Yeh optional condition hoti hai  
- Agar `should` clause match hoti hai toh document ka score badh jata hai  
- **Agar ek `must` ya `filter` clause diya gaya ho, toh `should` match karna zaroori nahi hota**  
- **Agar sirf `should` clauses ho toh kam se kam ek match hona chahiye**  

Example:

```json
{
  "query": {
    "bool": {
      "should": [
        { "match": { "description": "fast" } },
        { "match": { "description": "scalable" } }
      ]
    }
  }
}
```

📌 **Expected Output** – Dono me se koi bhi ek `description` match karega toh document select hoga. Agar dono match kare toh score badh jayega.  

---

### 🚫 `must_not` Clause  

- Yeh condition **match nahi honi chahiye**  
- Yeh ek document ko **exclude** karne ke kaam aati hai  
- Example:

```json
{
  "query": {
    "bool": {
      "must_not": [
        { "match": { "status": "inactive" } }
      ]
    }
  }
}
```

📌 **Expected Output** – Sirf wahi documents aayenge jisme `"status": "inactive"` **nahi hoga**.  

---

## ⚖ 4. Difference Between `must` & `filter`  

| Feature | `must` | `filter` |
|---------|--------|---------|
| **Matching Required?** | Haan | Haan |
| **Affects Scoring?** | Haan | Nahin |
| **Performance** | Slow (Scoring ke wajah se) | Fast (No scoring) |

Agar tumhe **filtering** chahiye bina scoring ke, toh **`filter`** use karna better hoga!  

---

## 💡 5. Use Cases of `bool` Query  

✅ **E-Commerce Filtering** – `"category": "electronics"` hona chahiye (filter) aur `"brand": "Apple"` hona chahiye (must)  
✅ **Job Search** – `"skills": "Java"` hona chahiye (must) aur `"remote": "true"` optional ho (should)  
✅ **Blacklist System** – `"user_status": "banned"` match nahi karna chahiye (must_not)  

---

## 📝 6. Examples with Expected Output  

### **Example 1 – Find active users in 'IT' department with good rating**  

```json
{
  "query": {
    "bool": {
      "must": [
        { "match": { "department": "IT" } }
      ],
      "filter": [
        { "term": { "status": "active" } }
      ],
      "should": [
        { "range": { "rating": { "gte": 4.5 } } }
      ],
      "must_not": [
        { "match": { "role": "intern" } }
      ]
    }
  }
}
```

📌 **Expected Output** –  
- **IT department** ke active users aayenge  
- **Interns** ko exclude kar diya jayega  
- **Rating >= 4.5** wale users ka score zyada hoga  

---

## 🎯 7. Performance Optimization Tips  

1️⃣ **Use `filter` instead of `must` for exact match conditions** – Kyunki filter caching use karta hai.  
2️⃣ **Avoid too many `should` clauses** – Performance impact ho sakta hai.  
3️⃣ **Use `must_not` carefully** – Kyunki yeh costly ho sakti hai large datasets par.  
4️⃣ **Test queries with `_explain` API** – Ye batata hai kaise scoring aur matching ho rahi hai.  

---

## 🔗 8. Related Queries  

- **`match` Query** – Text search ke liye  
- **`term` Query** – Exact match ke liye  
- **`range` Query** – Numerical ya date-based filtering ke liye  

---

## 🎬 Conclusion  

Elasticsearch me `bool` query ek **powerful tool** hai jo tumhe complex search conditions apply karne me help karti hai. Agar tum **performance aur accuracy** dono optimize karna chahte ho toh **`must`, `filter`, `should`, `must_not`** ka sahi combination use karna zaroori hai! 🚀  

