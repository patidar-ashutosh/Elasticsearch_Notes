# 🗑️ **Deleting Documents in Elasticsearch**  

## 📌 **Table of Contents**  
1. 🔹 [Introduction](#introduction)  
2. 🛠️ [Deleting a Single Document](#deleting-a-single-document)  
3. 🚀 [Verifying Deletion](#verifying-deletion)  
4. 📝 [Deleting Documents by Query](#deleting-documents-by-query)  
5. 🎯 [Best Practices](#best-practices)  

---

## 🔹 **Introduction**  
Ab tak tum **documents ko index, update, aur replace karna seekh chuke ho**.  

Ab **documents delete karna seekhenge**, jo kaafi **simple** process hai.  

✔️ **Elasticsearch REST API ka intuitive design hai, isliye delete operation bhi asaan hai.**  
✔️ **Agar tumhe ek specific document delete karna hai, to sirf HTTP verb change karna hoga.**  

---

## 🛠️ **Deleting a Single Document**  

Agar tumhare paas ek **document hai `id: 100`** aur tumhe use delete karna hai, to ye query use kar sakte ho:  

✅ **Delete Query:**  
```json
DELETE /products/_doc/100
```

✅ **Response:**  
```json
{
  "_index": "products",
  "_id": "100",
  "result": "deleted"
}
```

🔹 **Kya hoga?**  
✔️ Elasticsearch **document ko delete kar dega**.  
✔️ **Agar document exist nahi karta, to `"result": "not_found"` return karega.**  

---

## 🚀 **Verifying Deletion**  

Agar tumhe check karna hai ki document delete ho gaya ya nahi, to `GET` query se verify kar sakte ho:  

✅ **Check if Document Exists:**  
```json
GET /products/_doc/100
```

✅ **Response:**  
```json
{
  "found": false
}
```

👆 `"found": false"` ka matlab hai ki **document delete ho chuka hai**.  

---

## 📝 **Deleting Documents by Query**  

Agar tumhe **multiple documents delete karne hain jo ek specific condition match karte hain**, to tum `DELETE BY QUERY` use kar sakte ho.  

✅ **Example: Delete all products where `in_stock` is 0**  
```json
POST /products/_delete_by_query
{
  "query": {
    "match": {
      "in_stock": 0
    }
  }
}
```

✅ **Response:**  
```json
{
  "took": 45,
  "deleted": 10,
  "failures": []
}
```

👆 `"deleted": 10"` ka matlab hai **10 documents delete ho gaye**.  

⚠️ **Important:**  
✔️ **Yeh bulk delete hai, lekin ek ek document individually delete hota hai (slow process ho sakta hai).**  
✔️ **Agar index refresh setting `"refresh_interval": -1"` set hai, to deletion turant reflect nahi hoti.**  

---

## 🎯 **Best Practices**  

✔️ **Agar tumhe delete operation frequently karna hai, to soft deletes ya versioning ka use karo.**  
✔️ **Bulk delete karte waqt, ensure karo ki query sahi hai, warna galat documents delete ho sakte hain.**  
✔️ **Agar query-based deletion use kar rahe ho, to pehle `GET _search` query se verify kar lo ki kaunse documents match ho rahe hain.**  
✔️ **Delete operation ke baad "refresh" setting check karo, taaki deletion turant reflect ho.**  

---

