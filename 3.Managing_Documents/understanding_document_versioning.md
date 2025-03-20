# 📌 **Understanding Document Versioning in Elasticsearch**  

Elasticsearch me **document versioning** ka concept hota hai, lekin ye ek **simple form of versioning** hai.  
⚠️ **Iska revision history se koi lena dena nahi hota** – tum **past versions wapas nahi la sakte**.  
Lekin phir bhi versioning ka **kuch important use-case** hai jo samajhna zaroori hai!  

---

## 📖 **Table of Contents**  
1️⃣ **🔍 Document Versioning Kya Hota Hai?**  
2️⃣ **📊 Internal Versioning (Default)**  
3️⃣ **🔄 Versioning in Update & Delete Operations**  
4️⃣ **🔗 Internal vs External Versioning**  
5️⃣ **⚡ Optimistic Locking aur Versioning ka Role**  
6️⃣ **✅ Conclusion**  

---

## **1️⃣ 🔍 Document Versioning Kya Hota Hai?**  
Jab bhi tum **Elasticsearch me koi document index karte ho**, to uske saath ek **`_version` metadata field** automatically store hoti hai.  

✔ **Basic Rules:**  
- **Pehli baar document create hota hai to `_version = 1` hota hai.**  
- **Har update pe `_version` increment hota hai (`+1`).**  
- **Agar document delete hota hai, to `_version` ko 60 seconds tak retain kiya jata hai.**  

📌 **Important:**  
✔ **Ye versioning document ke past versions track nahi karti**.  
✔ **Sirf latest version store hota hai** (Tum **purane versions wapas nahi la sakte**).  

---

## **2️⃣ 📊 Internal Versioning (Default)**
🔹 Elasticsearch **default me internal versioning use karta hai**.  
🔹 `_version` **field automatically maintain hoti hai**, aur tum usko response me dekh sakte ho.  

### **Example: Internal Versioning**
```http
# Pehla document create karte hain
PUT /products/_doc/1
{
  "name": "Laptop",
  "brand": "Dell",
  "price": 75000
}
```
🔹 **Response:**  
```json
{
  "_index": "products",
  "_id": "1",
  "_version": 1,
  "result": "created"
}
```
➡ **`_version: 1` indicate karta hai ki document pehli baar index hua hai.**  

---

## **3️⃣ 🔄 Versioning in Update & Delete Operations**
Jab tum document update ya delete karte ho, to `_version` automatically increment hota hai.  

### **Update Example**
```http
POST /products/_update/1
{
  "doc": {
    "price": 72000
  }
}
```
🔹 **Response:**  
```json
{
  "_index": "products",
  "_id": "1",
  "_version": 2,
  "result": "updated"
}
```
➡ **Update hone ke baad `_version: 2` ho gaya!**  

### **Delete Example**
```http
DELETE /products/_doc/1
```
➡ **Ab document delete ho gaya, lekin Elasticsearch 60 seconds tak iska `_version` retain karega.**  

Agar **60 sec ke andar same ID ka naya document index kiya** to version increment hoga,  
Agar **60 sec ke baad create kiya** to `_version = 1` se reset hoga.  

---

## **4️⃣ 🔗 Internal vs External Versioning**
Elasticsearch me **2 tarah ki versioning hoti hai**:  

| **Type**       | **Kaise Work Karta Hai?** |
|---------------|--------------------------|
| **Internal**  | Elasticsearch automatically `_version` track karta hai. (Default behavior) |
| **External**  | Tum apni **database ya kisi aur system** me version track karke Elasticsearch ko bata sakte ho. |

### **Example: External Versioning**
```http
PUT /products/_doc/1?version=5&version_type=external
{
  "name": "Smartphone",
  "brand": "Samsung",
  "price": 50000
}
```
➡ **Yaha `_version = 5` explicitly diya gaya hai.**  
➡ **Ye tabhi accept hoga agar jo current `_version` hai wo 5 se chhota ho.**  

---

## **5️⃣ ⚡ Optimistic Locking aur Versioning ka Role**  
✔ **Pehle versioning ka use Optimistic Locking ke liye hota tha**  
✔ **Optimistic Locking ka matlab hai ki jab multiple users ek document ko modify kar rahe ho, to overwrite hone se bachaya jaye.**  
✔ **Lekin ab Elasticsearch me primary terms aur sequence numbers use kiye jate hain, jo zyada efficient hain.**  

👉 **Is wajah se `_version` field ka major use nahi hota**, lekin ye abhi bhi **legacy systems me kaam aati hai**.  

---

## **✅ Conclusion**
1️⃣ **Elasticsearch sirf latest version store karta hai.**  
2️⃣ **_version field automatically maintain hoti hai.**  
3️⃣ **Document update hone pe version badhta hai.**  
4️⃣ **Agar document delete ho jaye, to bhi version 60 sec tak retain hota hai.**  
5️⃣ **External versioning me tum khud version define kar sakte ho.**  

🔥 **Ab `_version` field ka major use nahi hota**, kyunki **primary terms aur sequence numbers ne isko replace kar diya hai.**  

---

