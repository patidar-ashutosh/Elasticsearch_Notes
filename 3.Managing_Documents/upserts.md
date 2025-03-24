# 📜 **Upserts in Elasticsearch**  

## 📌 **Table of Contents**  

1️⃣ **[🔹 Introduction](#1)**  
2️⃣ **[⚡ What is Upsert?](#2)**  
3️⃣ **[📝 Basic Upsert Example](#3)**  
4️⃣ **[🛠️ How Upsert Works](#4)**  
5️⃣ **[🚀 Using Parameters in Upserts](#5)**  
6️⃣ **[🎯 Best Practices](#6)**  

---

## 🔹 **Introduction**  <a id="1"></a>
Elasticsearch me **documents ko update karne ka ek aur tareeka** hai, jise **upsert** kehte hain.  

✔️ Agar **document exist karta hai**, to **update ho jata hai**.  
✔️ Agar **document exist nahi karta**, to **naya document insert ho jata hai**.  
✔️ **Ek hi query me update aur insert dono handle ho jata hai**.  

Agar tumhe **conditionally update ya insert** karna ho, to **upsert ka use kar sakte ho**.  

---

## ⚡ **What is Upsert?**  <a id="2"></a>

🔹 **Upsert ka matlab hai:**  
✔️ **"Update + Insert"**  
✔️ Document **hai to update hoga**, nahi hai to **insert ho jayega**.  
✔️ Elasticsearch me **Update API** ka use karke **upsert** implement kiya jata hai.  

🔹 **Upsert ka benefit:**  
✔️ **Alag-alag query likhne ki zaroorat nahi hoti**.  
✔️ **Performance better hoti hai**, kyunki **sirf ek request me kaam ho jata hai**.  
✔️ **Dynamic data ke saath kaam karne me useful hota hai**.  

---

## 📝 **Basic Upsert Example**  <a id="3"></a>

🔹 **Maan lo tumhe `id: 101` ka document update karna hai.**  
🔹 **Agar wo pehle se exist nahi karta, to naye data se insert karna hai.**  

✅ **Query:**  
```json
POST /products/_update/101
{
  "script": {
    "source": "ctx._source.in_stock += 1"
  },
  "upsert": {
    "name": "Toaster",
    "price": 20,
    "in_stock": 5
  }
}
```

✅ **Agar document `101` exist nahi karta, to `upsert` ka data insert hoga:**  
```json
{
  "_index": "products",
  "_id": "101",
  "result": "created"
}
```

✅ **Agar document `101` already exist karta hai, to `script` execute hoga:**  
```json
{
  "_index": "products",
  "_id": "101",
  "result": "updated"
}
```

👆 `"upsert"` me diya hua data tabhi insert hota hai **jab document exist nahi karta**.  
👆 `"script"` sirf tab run hota hai **jab document already exist karta hai**.  

---

## 🛠️ **How Upsert Works?**  <a id="4"></a>

🔹 **Scenario 1:** (Agar document exist nahi karta)  
- **`upsert` ka content insert ho jayega**  
- **Response: `"result": "created"`**  

🔹 **Scenario 2:** (Agar document exist karta hai)  
- **`script` execute hoga**  
- **Response: `"result": "updated"`**  

🔹 **Example Flow:**  

1️⃣ **Pehli baar query run karoge (document exist nahi karta hai):**  
   - `upsert` ka data insert ho jayega (`"in_stock": 5`).  
2️⃣ **Dubara query run karoge (document exist karta hai):**  
   - `script` execute hoga (`in_stock` **+1 ho jayega, ab `in_stock: 6` ho jayega**).  

---

## 🚀 **Using Parameters in Upserts**  <a id="5"></a>

Agar tum **dynamic value se update karna chahte ho**, to **parameters use kar sakte ho**.  

✅ **Query:**  
```json
POST /products/_update/101
{
  "script": {
    "source": "ctx._source.in_stock += params.amount",
    "params": {
      "amount": 3
    }
  },
  "upsert": {
    "name": "Toaster",
    "price": 20,
    "in_stock": 5
  }
}
```

✅ **Agar document exist nahi karega, to insert hoga:**  
```json
{
  "_index": "products",
  "_id": "101",
  "result": "created"
}
```

✅ **Agar document exist karega, to update hoga (`in_stock` +3 ho jayega):**  
```json
{
  "_index": "products",
  "_id": "101",
  "result": "updated"
}
```

👆 `"params": { "amount": 3 }` se **dynamic value set kar sakte ho**.  
👆 `"ctx._source.in_stock += params.amount"` se **in_stock dynamically increase hoga**.  

---

## 🎯 **Best Practices**  <a id="6"></a>

✔️ **Agar document insert karna ho aur update bhi karna ho, to Upsert best option hai**.  
✔️ **Frequent upserts avoid karo**, kyunki Elasticsearch **document ko immutable treat karta hai**.  
✔️ **Script me `params` ka use karo**, taaki query **reusable aur dynamic** ho.  
✔️ **Agar sirf update chahiye aur insert nahi karna, to normal Update API ka use karo**.  

---

