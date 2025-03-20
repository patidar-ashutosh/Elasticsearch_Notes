# 📌 Pagination

## 📖 Table of Contents  

1️⃣ **🔍 Introduction: What is Pagination?**  
2️⃣ **⚙️ How Pagination Works in Elasticsearch**  
3️⃣ **📌 Example Queries: Implementing Pagination**  
4️⃣ **📊 Calculation: Total Pages & Offsets**  
5️⃣ **⚠️ Deep Pagination Limitations & `search_after`**  
6️⃣ **🔄 Pagination vs Cursors: Key Differences**  
7️⃣ **🛠️ Best Practices for Efficient Pagination**  
8️⃣ **🔚 Summary & Key Takeaways**  

---

## 1️⃣ 🔍 Introduction: What is Pagination?  

🔹 **Pagination ka matlab hota hai search results ko multiple pages me distribute karna, taaki ek hi request me bohot zyada data na aaye.**  
🔹 Tumhe **total pages calculate karne hote hain** aur har page ke liye **correct offset set karna hota hai.**  
🔹 Elasticsearch me **pagination `size` aur `from` parameters** se hoti hai.  

---

## 2️⃣ ⚙️ How Pagination Works in Elasticsearch  

✅ **`size` (Limit):** Ek page me kitne documents fetch karne hain.  
✅ **`from` (Offset):** Kitne documents **skip karne hain** before fetching the results.  

➡️ Example: **Agar tumhe har page me 10 results dikhane hain aur dusre page ka data lana hai, to tum first 10 records skip karoge (`from: 10`).**  

---

## 3️⃣ 📌 Example Queries: Implementing Pagination  

### **🚀 First Page (0-9 records)**  
```json
GET my_index/_search
{
  "from": 0,
  "size": 10,
  "query": {
    "match_all": {}
  }
}
```
✔️ **Yeh query first 10 documents fetch karegi.**  

---

### **🚀 Second Page (10-19 records)**  
```json
GET my_index/_search
{
  "from": 10,
  "size": 10,
  "query": {
    "match_all": {}
  }
}
```
✔️ **First 10 records skip honge, aur agle 10 records fetch honge.**  

---

### **🚀 Third Page (20-29 records)**  
```json
GET my_index/_search
{
  "from": 20,
  "size": 10,
  "query": {
    "match_all": {}
  }
}
```
✔️ **20th record se next 10 results fetch honge.**  

---

## 4️⃣ 📊 Calculation: Total Pages & Offsets  

✔️ Total number of pages calculate karne ka formula:  
\[
\text{Total Pages} = \text{ceil} \left(\frac{\text{Total Hits}}{\text{Page Size}}\right)
\]

➡️ **Example:**  
Agar **137 results hain aur har page me 10 results chahiye**, to:  
\[
\frac{137}{10} = 13.7 \Rightarrow \text{Ceil}(13.7) = 14 \text{ pages}
\]

✔️ **Offset calculation formula:**  
\[
\text{Offset} = (\text{Page Number} - 1) \times \text{Page Size}
\]

➡️ **Example:**  
Agar **Page Number = 6 aur Page Size = 10**, to:  
\[
(6 - 1) \times 10 = 50
\]  
Matlab **50 records skip karne hain aur agle 10 fetch karne hain.**  

---

## 5️⃣ ⚠️ Deep Pagination Limitations & `search_after`  

📌 **Elasticsearch me deep pagination ka limit 10,000 results tak hota hai.**  
✔️ Agar **tumhe 10,000+ results fetch karne hain**, to **`search_after` use karo**, jo zyada efficient hota hai.  

➡️ **Why?**  
- **`from` parameter deep queries me slow hota hai** kyunki Elasticsearch har offset ke liye sare records traverse karta hai.  
- **`search_after` last result ke sort key ko use karke next page fetch karta hai**, jo fast hota hai.  

✔️ **Example of `search_after` (recommended for large datasets):**  
```json
GET my_index/_search
{
  "size": 10,
  "query": {
    "match_all": {}
  },
  "sort": [{ "timestamp": "asc" }],
  "search_after": [1687000000]
}
```
➡️ **Yeh approach large-scale pagination ke liye best hai.**  

---

## 6️⃣ 🔄 Pagination vs Cursors: Key Differences  

📌 **Elasticsearch queries stateless hoti hain**, matlab **har query fresh results return karti hai.**  

➡️ **Difference from Relational Databases (SQL Cursors):**  
| Feature           | SQL Cursor | Elasticsearch Pagination |
|-------------------|------------|--------------------|
| **Statefulness**  | Stateful (results persist) | Stateless (fresh query each time) |
| **Consistency**   | Fixed results | May change if data updates |
| **Performance**   | Efficient | Slows down with deep pagination |

➡️ **Example Scenario:**  
1. **User Page 1 pe rehta hai for 1 min.**  
2. **New document add hota hai jo Page 1 pe aana chahiye.**  
3. **Agar user Page 2 pe jata hai, to duplicate results mil sakte hain (kyunki Page 1 change ho gaya tha).**  

✔️ **Solution:** `search_after` ya `scroll API` use karo to avoid such issues.  

---

## 7️⃣ 🛠️ Best Practices for Efficient Pagination  

✅ **`size` aur `from` ka use sirf small pagination ke liye karo.**  
✅ **Deep pagination ke liye `search_after` ya `scroll API` prefer karo.**  
✅ **Sorting implement karo taaki results consistent rahein.**  
✅ **Pagination queries ko cache mat karo, kyunki results change ho sakte hain.**  

---

## 8️⃣ 🔚 Summary & Key Takeaways  

✅ **`size` se page ka data limit hota hai, aur `from` se offset set hota hai.**  
✅ **Total pages = Total Hits ÷ Page Size (rounded up).**  
✅ **Deep pagination ke liye `search_after` better hai, kyunki `from` slow ho jata hai.**  
✅ **Elasticsearch ke queries stateless hote hain, isliye results change ho sakte hain.**  
✅ **Efficient pagination ke liye sorting zaroori hai.**  

---

