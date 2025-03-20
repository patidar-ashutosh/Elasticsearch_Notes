# **📌 Sorting in OpenSearch**  

## **📌 Introduction**  
`sort` ka use search results ko **ascending ya descending order me arrange** karne ke liye hota hai.  
- Default behavior me OpenSearch **relevance score (`_score`)** ke basis pe results return karta hai.  
- Agar tumhe **custom sorting** chahiye jaise **price, date, alphabetically, etc.**, to `sort` parameter ka use hota hai.  

📌 **Use Cases:**  
- **Price-based sorting:** Sabse mehenga ya sasta product dikhana.  
- **Date-based sorting:** Naye ya purane events pehle dikhana.  
- **Alphabetical sorting:** A to Z ya Z to A order me arrange karna.  

---

## **📌 Syntax (Short & Simple)**  
```json
GET index_name/_search
{
  "sort": [
    { "field_name": { "order": "asc" } }
  ],
  "query": {
    "match_all": {}
  }
}
```
🔹 **index_name** → Jis index me search karna hai.  
🔹 **field_name** → Jis field pe sorting karni hai.  
🔹 **order** → `"asc"` (ascending) ya `"desc"` (descending).  

---

## **✅ Example**  
Hum ek index **products** banayenge jisme sorting apply karenge.

### **Step 1: Index & Data Creation**
```json
PUT products
{
  "mappings": {
    "properties": {
      "name": { "type": "text", "fields": { "keyword": { "type": "keyword" } } },
      "price": { "type": "integer" },
      "release_date": { "type": "date" }
    }
  }
}
```

```json
POST products/_bulk
{ "index": { "_id": 1 } }
{ "name": "iPhone 13", "price": 799, "release_date": "2023-09-14" }
{ "index": { "_id": 2 } }
{ "name": "Samsung Galaxy S21", "price": 699, "release_date": "2021-01-29" }
{ "index": { "_id": 3 } }
{ "name": "OnePlus 9", "price": 729, "release_date": "2021-03-23" }
{ "index": { "_id": 4 } }
{ "name": "Google Pixel 6", "price": 599, "release_date": "2021-10-19" }
{ "index": { "_id": 5 } }
{ "name": "Sony Xperia 5", "price": 899, "release_date": "2022-09-01" }
```

---

## **✅ Example 1: Price ke Basis pe Ascending Order (Sasta Pehle)**
```json
GET products/_search
{
  "sort": [
    { "price": { "order": "asc" } }
  ],
  "query": {
    "match_all": {}
  }
}
```
### **🟢 Expected Output**
```json
{
  "hits": {
    "hits": [
      { "_id": "4", "_source": { "name": "Google Pixel 6", "price": 599 } },
      { "_id": "2", "_source": { "name": "Samsung Galaxy S21", "price": 699 } },
      { "_id": "3", "_source": { "name": "OnePlus 9", "price": 729 } },
      { "_id": "1", "_source": { "name": "iPhone 13", "price": 799 } },
      { "_id": "5", "_source": { "name": "Sony Xperia 5", "price": 899 } }
    ]
  }
}
```
✔ **Sabse sasta product sabse pehle dikh raha hai.**  

---

## **✅ Example 2: Price ke Basis pe Descending Order (Mehenga Pehle)**
```json
GET products/_search
{
  "sort": [
    { "price": { "order": "desc" } }
  ],
  "query": {
    "match_all": {}
  }
}
```
✔ **Isme sabse mehenga product sabse pehle aayega.**  

---

## **✅ Example 3: Date ke Basis pe Sorting (Naya ya Purana Pehle)**  
### **Newest First (`desc`)**
```json
GET products/_search
{
  "sort": [
    { "release_date": { "order": "desc" } }
  ],
  "query": {
    "match_all": {}
  }
}
```
✔ **Isme sabse latest product pehle aayega.**  

---

## **✅ Example 4: Alphabetical Sorting (A-Z ya Z-A)**
**Agar tumhe `name` field pe sorting karni ho, to `.keyword` field ka use karna padega:**  

### **A to Z Sorting (`asc`)**
```json
GET products/_search
{
  "sort": [
    { "name.keyword": { "order": "asc" } }
  ],
  "query": {
    "match_all": {}
  }
}
```
✔ **Isme names A to Z order me arrange honge.**  

### **Z to A Sorting (`desc`)**
```json
GET products/_search
{
  "sort": [
    { "name.keyword": { "order": "desc" } }
  ],
  "query": {
    "match_all": {}
  }
}
```
✔ **Isme names Z to A order me arrange honge.**  

---

## **✅ Example 5: Multiple Fields ke Basis pe Sorting**
Agar tumhe pehle **price** ke basis pe sorting karni hai aur agar price same ho to **date** ke basis pe sort karna hai:  
```json
GET products/_search
{
  "sort": [
    { "price": { "order": "asc" } },
    { "release_date": { "order": "desc" } }
  ],
  "query": {
    "match_all": {}
  }
}
```
✔ **Sabse sasta product pehle aayega, aur agar price same hai to naya product pehle aayega.**  

---

## **📌 Key Takeaways**  
✔ **Sorting ke liye `sort` parameter use hota hai.**  
✔ **Ascending (`asc`) ya descending (`desc`) order specify kar sakte ho.**  
✔ **Text fields pe sorting karne ke liye `.keyword` version ka use hota hai.**  
✔ **Multiple fields ke basis pe sorting possible hai.**  
✔ **By default, OpenSearch `_score` ke basis pe sorting karta hai, isliye agar query-based sorting nahi chahiye to `sort` explicitly likhna zaroori hai.**  

