# 📌 Specifying the Result Size

## 📖 Table of Contents  

1️⃣ **[🔍 Introduction: Why Control Result Size?](#1)**  
2️⃣ **[⚙️ Method 1: Using Query Parameter (`size` in URL)](#2)**  
3️⃣ **[📌 Method 2: Using Request Body (`size` in Query DSL)](#3)**  
4️⃣ **[📊 Default Behavior & Pagination Impact](#4)**  
5️⃣ **[🛠️ Best Practices & Performance Considerations](#5)**  
6️⃣ **[🔚 Summary & Key Takeaways](#6)**  

---

## 1️⃣ 🔍 Introduction: Why Control Result Size?  <a id="1"></a>

🔹 By default, **Elasticsearch sirf 10 documents return karta hai**, lekin kabhi-kabhi hume **zyada ya kam results** chahiye hote hain.  
🔹 **Solution:** `size` parameter ka use karke hum **custom number of results fetch kar sakte hain.**  

### **🚀 Key Use Cases:**  
✔️ **Pagination ke liye limited records fetch karna.**  
✔️ **Performance optimize karne ke liye unnecessary results ko avoid karna.**  
✔️ **Query results ko control karna (e.g., top 5 matches, latest 3 records, etc.).**  

---

## 2️⃣ ⚙️ Method 1: Using Query Parameter (`size` in URL)  <a id="2"></a>

Agar tum **Kibana Console ya API request me** directly **URL me `size` parameter** add kar do, to sirf utne hi results return honge.  

### **🚀 Example (Using Query Parameter in URL)**
```json
GET my_index/_search?size=2
{
  "_source": false,
  "query": {
    "match_all": {}
  }
}
```
🛠️ **Response:**  
```json
{
  "hits": {
    "total": 8,  
    "hits": [
      { "_id": "1" },
      { "_id": "2" }
    ]
  }
}
```
✔️ **Sif 2 results return ho rahe hain, lekin `total` key ab bhi full match count dikhata hai (Pagination ke liye useful).**  

---

## 3️⃣ 📌 Method 2: Using Request Body (`size` in Query DSL)  <a id="3"></a>

Agar tum **Elasticsearch Query DSL** (JSON request body) use kar rahe ho, to `size` parameter ko **request body me bhi specify kar sakte ho.**  

### **🚀 Example (Using `size` in Request Body)**
```json
GET my_index/_search
{
  "size": 2,
  "_source": false,
  "query": {
    "match_all": {}
  }
}
```
🛠️ **Response:**  
```json
{
  "hits": {
    "total": 8,
    "hits": [
      { "_id": "1" },
      { "_id": "2" }
    ]
  }
}
```
✔️ **Same output as URL query parameter method!**  

🔹 **Difference kya hai?**  
- URL me `size` use karna **quick queries** ke liye best hai.  
- **Request Body me `size` add karna zyada readable aur maintainable hota hai,** especially agar tum **complex queries** likh rahe ho.  

---

## 4️⃣ 📊 Default Behavior & Pagination Impact  <a id="4"></a>

✅ **By Default:** Elasticsearch sirf **10 results return karta hai.**  
✅ **`size` parameter specify karna optional hai, sirf tab use karo jab tumhe default (10) se alag result count chahiye.**  

🔹 **Pagination ke liye:**  
- **Agar tumhe 2nd page ke results chahiye, to `from` parameter use karo.**  
- **Example:** Agar tum first **10 results ke baad ke 5 results fetch karna chahte ho:  
```json
GET my_index/_search
{
  "from": 10,
  "size": 5,
  "query": {
    "match_all": {}
  }
}
```
✔️ **Yeh query 11th se 15th records return karegi.**  

---

## 5️⃣ 🛠️ Best Practices & Performance Considerations  <a id="5"></a>

❌ **Bohot bade `size` values avoid karo** – Large queries Elasticsearch cluster pe load dal sakti hain.  
✅ **Paginated requests use karo** – `size` ke sath `from` parameter use karna best practice hai.  
✅ **Aggregation queries ke liye `size` avoid karo** – Aggregations me direct document fetch karne ki jagah summary statistics calculate hoti hai.  

---

## 6️⃣ 🔚 Summary & Key Takeaways  <a id="6"></a>

✅ **Default result size 10 hota hai.**  
✅ **URL Query Parameter (`?size=2`) aur Request Body (`"size": 2`) dono methods se result size control kar sakte ho.**  
✅ **Agar tum paginated queries likh rahe ho to `from` ke sath `size` use karo.**  
✅ **Performance optimize karne ke liye small `size` values rakhna better hai.**  

---

