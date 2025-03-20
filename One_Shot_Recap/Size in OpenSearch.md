# **Size in OpenSearch**  

## **📌 Introduction**  
**Size** parameter OpenSearch me **maximum number of search results** ko control karne ke liye use hota hai. By default, OpenSearch **sirf 10 results return karta hai**, lekin **size parameter** ka use karke tum is limit ko badha sakte ho.  

📌 **Use Case:**  
- Agar tumhe **default se zyada documents retrieve karne hain**, to `size` set kar sakte ho.  
- **Pagination implement** karne ke liye `size` ko `from` ke saath use karte hain.  

---

## **📌 Syntax (Short & Simple)**  
```json
GET index_name/_search
{
  "size": number,
  "query": {
    "match_all": {}
  }
}
```
🔹 **index_name** → Jis index me search karna hai.  
🔹 **size** → Kitne documents return karne hain (default: **10**).  

---

## **✅ Example**  
Hum ek index **products** banayenge aur uspe **size parameter** ka use karenge.

### **Step 1: Index & Data Creation**
```json
PUT products
{
  "mappings": {
    "properties": {
      "name": { "type": "text" }
    }
  }
}
```

```json
POST products/_bulk
{ "index": { "_id": 1 } }
{ "name": "iPhone 13" }
{ "index": { "_id": 2 } }
{ "name": "Samsung Galaxy S21" }
{ "index": { "_id": 3 } }
{ "name": "OnePlus 9" }
{ "index": { "_id": 4 } }
{ "name": "Google Pixel 6" }
{ "index": { "_id": 5 } }
{ "name": "Sony Xperia 5" }
```

---

### **Step 2: Default Size (Without Setting `size`)**
```json
GET products/_search
{
  "query": {
    "match_all": {}
  }
}
```
🔹 **Default me sirf 10 results return honge**, agar documents 10 se zyada hain to bhi.  

---

### **Step 3: Custom Size Example**
```json
GET products/_search
{
  "size": 3,
  "query": {
    "match_all": {}
  }
}
```

### **🟢 Expected Output**
```json
{
  "hits": {
    "total": { "value": 5 },
    "hits": [
      { "_id": "1", "_source": { "name": "iPhone 13" } },
      { "_id": "2", "_source": { "name": "Samsung Galaxy S21" } },
      { "_id": "3", "_source": { "name": "OnePlus 9" } }
    ]
  }
}
```
✔ **Sirf 3 results return honge kyunki `size: 3` set kiya hai.**  
❌ **Baaki documents skip ho jayenge agar pagination use nahi kiya.**  

---

## **🔹 Pagination Using `from` & `size`**
Agar tum **next page ke documents retrieve** karna chahte ho, to **`from` parameter** ke saath `size` ka use karo.  

```json
GET products/_search
{
  "from": 2,
  "size": 2,
  "query": {
    "match_all": {}
  }
}
```
✔ **2nd page ke 2 documents milenge** (skip first 2 documents).  

---

## **📌 Key Takeaways**  
✔ **`size` default 10 hota hai, tum ise badha sakte ho.**  
✔ **Large datasets pe size limit set karna performance ke liye important hai.**  
✔ **Pagination ke liye `from` aur `size` ka saath use hota hai.**  
✔ **Agar `size` bohot bada set kiya, to performance slow ho sakti hai.**  

