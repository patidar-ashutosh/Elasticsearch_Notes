# 📌 Elasticsearch Bulk API

## 📖 Table of Contents
1. 🔍 **Introduction to Bulk API**
2. 🏗 **Why Use Bulk API?**
3. 🛠 **Bulk API Syntax and Structure**
4. 📜 **Indexing Multiple Documents Using Bulk API**
5. 📝 **Updating Documents in Bulk**
6. ❌ **Deleting Documents in Bulk**
7. ⚡ **Performance Considerations & Best Practices**
8. 🛑 **Common Errors & Troubleshooting**
9. 🎯 **Conclusion**

---

## 🔍 1. Introduction to Bulk API
Elasticsearch **Bulk API** ek powerful feature hai jo tumhe ek hi request me multiple documents ko **index**, **update**, aur **delete** karne ki suvidha deta hai. Yeh API large-scale indexing aur updates ke liye kaafi efficient hoti hai.

💡 **Key Use Case:** Jab tumhare paas **bahut saare** documents ho aur tum ek-ek karke request bhejne ki jagah **batch processing** karna chahte ho.

---

## 🏗 2. Why Use Bulk API?
Agar tum **single request** se har document ko index karoge, toh har ek ke liye HTTP overhead hoga, jo **performance slow** kar sakta hai. Bulk API ka use karne se:

✅ **Faster indexing** - Kyunki multiple documents ek hi request me process hote hain.
✅ **Reduced network overhead** - Har request ka HTTP overhead kam ho jata hai.
✅ **Better resource utilization** - Elasticsearch efficiently handle karta hai.

---

## 🛠 3. Bulk API Syntax and Structure
Bulk API ek **JSON payload** ko expect karta hai, jo multiple **action and data lines** se bana hota hai.

**Syntax Example:**
```json
{ "action": { "_index": "my_index", "_id": "1" } }
{ "field1": "value1", "field2": "value2" }
{ "action": { "_index": "my_index", "_id": "2" } }
{ "field1": "value3", "field2": "value4" }
```

💡 **Important:**
- Yeh **Newline Delimited JSON (NDJSON)** format hota hai.
- **Action line** batata hai ki document ke saath kya karna hai.
- **Data line** actual document hota hai.
- **Ek blank line end me nahi honi chahiye.**

---

## 📜 4. Indexing Multiple Documents Using Bulk API

### **Example: Insert Multiple Documents**
```sh
POST _bulk
{ "index": { "_index": "products", "_id": "1" } }
{ "name": "Laptop", "price": 50000 }
{ "index": { "_index": "products", "_id": "2" } }
{ "name": "Mouse", "price": 1500 }
```

**Expected Response:**
```json
{
  "took": 30,
  "errors": false,
  "items": [
    { "index": { "_index": "products", "_id": "1", "result": "created" } },
    { "index": { "_index": "products", "_id": "2", "result": "created" } }
  ]
}
```

🔹 **"errors": false** ka matlab hai ki saare documents successfully index ho gaye.

---

## 📝 5. Updating Documents in Bulk

Agar tumhe documents update karne hai, toh **update action** ka use karna hoga.

### **Example: Update Multiple Documents**
```sh
POST _bulk
{ "update": { "_index": "products", "_id": "1" } }
{ "doc": { "price": 52000 } }
{ "update": { "_index": "products", "_id": "2" } }
{ "doc": { "price": 1600 } }
```

**Expected Response:**
```json
{
  "took": 20,
  "errors": false,
  "items": [
    { "update": { "_index": "products", "_id": "1", "result": "updated" } },
    { "update": { "_index": "products", "_id": "2", "result": "updated" } }
  ]
}
```

✅ **"doc" key** ka use hota hai data update karne ke liye.

---

## ❌ 6. Deleting Documents in Bulk
Agar tum documents delete karna chahte ho toh **delete action** ka use kar sakte ho.

### **Example: Delete Multiple Documents**
```sh
POST _bulk
{ "delete": { "_index": "products", "_id": "1" } }
{ "delete": { "_index": "products", "_id": "2" } }
```

**Expected Response:**
```json
{
  "took": 10,
  "errors": false,
  "items": [
    { "delete": { "_index": "products", "_id": "1", "result": "deleted" } },
    { "delete": { "_index": "products", "_id": "2", "result": "deleted" } }
  ]
}
```

✅ Har delete operation ke liye ek confirmation milta hai.

---

## ⚡ 7. Performance Considerations & Best Practices

🚀 **Best Practices for Bulk API:**
- **Batch size ko optimize karo** - Ek batch me **5-15 MB** ka data bhejna recommended hai.
- **Use parallel processing** - Multiple bulk requests ko parallel me bhejna better hota hai.
- **Check errors** - Response me "errors": true ho to failed requests ko retry karo.
- **Avoid very large payloads** - Bahut bade JSON se memory aur speed issues ho sakte hain.

---

## 🛑 8. Common Errors & Troubleshooting

🚨 **Common Errors:**
1. **400 Bad Request** - JSON format galat hai ya blank line end me hai.
2. **413 Request Entity Too Large** - Payload size bahut bada hai, usko chhote batches me divide karo.
3. **429 Too Many Requests** - Rate limiting ho rahi hai, retries aur exponential backoff ka use karo.

---

## 🎯 9. Conclusion
Elasticsearch **Bulk API** ek **high-performance tool** hai jo tumhe **large-scale indexing, updating, aur deleting** ke liye madad karta hai. Isko sahi tarike se implement karna **performance aur efficiency** dono improve karta hai.

Agar tum **high-volume data ingestion** ya **batch processing** kar rahe ho, toh Bulk API **best choice** hai. 🚀

---
[Reference Video Link](https://youtu.be/SKY_oYz6628?si=HjIM4-e6rIQFI0Dy)
---