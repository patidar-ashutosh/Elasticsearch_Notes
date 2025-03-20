### 📌 Understanding `doc_values` in Elasticsearch  

Elasticsearch me `doc_values` ek important feature hai jo structured data (like keyword fields, numbers, dates, etc.) ko efficiently store aur retrieve karne ke liye use hota hai. Yeh mainly **sorting, aggregations, aur filtering** ko fast banata hai by storing field values in a columnar format on disk.  

---

## 📑 **Table of Contents**  

1️⃣ [Introduction to `doc_values`](#introduction-to-doc_values)  
2️⃣ [Why `doc_values` is important?](#why-doc_values-is-important)  
3️⃣ [How `doc_values` works internally?](#how-doc_values-works-internally)  
4️⃣ [Example: Using `doc_values`](#example-using-doc_values)  
5️⃣ [Disabling `doc_values`](#disabling-doc_values)  
6️⃣ [Conclusion](#conclusion)  

---

## 1️⃣ **Introduction to `doc_values`**  
<a id="introduction-to-doc_values"></a>  
By default, Elasticsearch me **numeric, date, aur keyword** fields ko `doc_values` ke saath store kiya jata hai.  

🔹 **Purpose:** `doc_values` ka main kaam hai **disk-based columnar storage** provide karna jisse aggregation aur sorting operations fast ho sake.  
🔹 **Default Behavior:** Ye automatically enabled hota hai **keyword, numeric, aur date fields** ke liye.  
🔹 **Alternative:** Agar `doc_values` disable hota hai, to Elasticsearch **in-memory fielddata** ka use karta hai, jo expensive aur slow hota hai.  

---

## 2️⃣ **Why `doc_values` is important?**  
<a id="why-doc_values-is-important"></a>  
Elasticsearch me searching fast hoti hai because of **inverted index**, but sorting aur aggregations me problem ho sakti hai.  

✅ **Fast Aggregations & Sorting** – Kyunki `doc_values` columnar format me store hota hai, aggregation aur sorting bahut fast hoti hai.  
✅ **Low Memory Usage** – Kyunki yeh data ko disk pe store karta hai, yeh **fielddata** se better hota hai jo memory consume karta hai.  
✅ **Efficient Filtering** – Range queries aur filtering operations bhi fast hote hain.  

---

## 3️⃣ **How `doc_values` works internally?**  
<a id="how-doc_values-works-internally"></a>  

Elasticsearch me `doc_values` ek **columnar data structure** use karta hai. Har field ke values alag-alag sorted format me store hote hain.  

🛠 **Working:**  
- Har field ka **column-wise representation** hota hai, unlike row-wise storage (like traditional databases).  
- Disk-based storage hoti hai, jo memory-efficient hoti hai.  
- Read operations fast hote hain kyunki field values sequentially store hote hain.  

📌 **Comparison Between Inverted Index & `doc_values`**  

| Feature               | Inverted Index         | `doc_values`          |
|----------------------|----------------------|----------------------|
| Purpose             | Fast searching       | Fast sorting & aggregations |
| Data Structure      | Token-based index    | Column-based index  |
| Storage            | Row-oriented (shards) | Column-oriented (disk) |
| Memory Usage       | High (for fielddata) | Low (disk-based) |

---

## 4️⃣ **Example: Using `doc_values`**  
<a id="example-using-doc_values"></a>  

### ✅ **Step 1: Create an Index with `doc_values`**  

```json
PUT my_index
{
  "mappings": {
    "properties": {
      "name": {
        "type": "keyword"
      },
      "age": {
        "type": "integer",
        "doc_values": true
      }
    }
  }
}
```

📝 **Explanation:**  
- `name` field **keyword** type hai, so `doc_values` **default** enabled hoga.  
- `age` field **integer** type hai, aur humne explicitly `doc_values: true` enable kiya hai.  

---

### ✅ **Step 2: Insert Sample Documents**  

```json
POST my_index/_bulk
{ "index": { "_id": 1 } }
{ "name": "Alice", "age": 25 }
{ "index": { "_id": 2 } }
{ "name": "Bob", "age": 30 }
{ "index": { "_id": 3 } }
{ "name": "Charlie", "age": 22 }
```

---

### ✅ **Step 3: Sorting using `doc_values`**  

```json
GET my_index/_search
{
  "sort": [
    { "age": "asc" }
  ]
}
```

📝 **How it Works?**  
- Elasticsearch **`doc_values`** ka use karke **age** field ko **disk-based columnar format** se fetch karega.  
- **In-memory processing ki zarurat nahi hogi**, jo isko faster banata hai.  

---

## 5️⃣ **Disabling `doc_values`**  
<a id="disabling-doc_values"></a>  

Agar tumhe `doc_values` disable karna ho, to aisa mapping define kar sakte ho:  

```json
PUT my_index
{
  "mappings": {
    "properties": {
      "age": {
        "type": "integer",
        "doc_values": false
      }
    }
  }
}
```

📌 **Kya Hoga Agar `doc_values` Disable Kar Diya Jaye?**  
🚫 Sorting, aggregations, aur filtering operations slow ho jayenge.  
🚫 Elasticsearch ko **fielddata** use karna padega, jo **RAM** me load hota hai aur memory leak issues create kar sakta hai.  

---

## 6️⃣ **Conclusion**  
<a id="conclusion"></a>  

✅ `doc_values` Elasticsearch ka ek powerful feature hai jo **sorting, filtering, aur aggregations** fast banata hai.  
✅ Yeh **disk-based columnar storage** use karta hai, jo **RAM-efficient** hota hai.  
✅ Default enabled hota hai **keyword, numeric, aur date** fields ke liye.  
✅ Agar disable kiya jaye, to Elasticsearch **in-memory fielddata** ka use karta hai, jo slow aur memory-intensive hota hai.  

---

💡 **Final Tip:**  
Hamesha `doc_values` enabled rakhna chahiye **sorting aur aggregations** ke liye. Agar koi field search aur aggregations dono ke liye chahiye, to **dual mapping (keyword + text)** ka use karna best practice hai! 🚀🔥

---
[Reference Video Link](https://youtu.be/6PmR0OnC9Q4?si=HT6OABBUZSvDnJxM)
---