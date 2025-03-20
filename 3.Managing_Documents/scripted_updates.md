# 📜 **Scripted Updates in Elasticsearch**  

## 📌 **Table of Contents**  
1. 🔹 [Introduction](#introduction)  
2. ⚡ [Why Use Scripted Updates?](#why-use-scripted-updates)  
3. 📝 [Basic Scripted Update Example](#basic-scripted-update-example)  
4. 🛠️ [Using Parameters in Scripts](#using-parameters-in-scripts)  
5. 🚀 [Conditional Updates with `noop`](#conditional-updates-with-noop)  
6. 🗑️ [Deleting a Document Using a Script](#deleting-a-document-using-a-script)  
7. 🎯 [Best Practices](#best-practices)  

---

## 🔹 **Introduction**  
Agar tum **document update** kar rahe ho aur kisi field ka value **decrease ya increase** karna hai **bina uska current value retrieve kiye**, to **scripted updates** ka use kar sakte ho.  

Elasticsearch me **scripting allow hoti hai**, jisme tum **custom logic likh sakte ho**, jaise:  
✔️ Kisi field ko **modify** karna  
✔️ **Conditional updates** likhna  
✔️ **Parameters pass** karna  
✔️ Document ko **delete** karna based on conditions  

---

## ⚡ **Why Use Scripted Updates?**  

🔹 **Without Scripted Update:**  
  - Pehle **document retrieve karo**  
  - Fir **value modify karo**  
  - Fir **update request bhejo**  
  - **Network latency badhta hai**  

🔹 **With Scripted Update:**  
  - **Sab kuch ek hi request me ho jata hai**  
  - **Performance better hoti hai**  

---

## 📝 **Basic Scripted Update Example**  

🔹 **Maan lo hume `in_stock` field ko `-1` karna hai.**  
🔹 **Hume uska pehle se value pata nahi hai, bas ek request me update karna hai.**  

✅ **Query:**  
```json
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock -= 1"
  }
}
```

✅ **Response:**  
```json
{
  "_index": "products",
  "_id": "100",
  "result": "updated"
}
```

👆 `"ctx._source"` ka matlab **document ka data**.  
👆 `"ctx._source.in_stock -= 1"` ka matlab **`in_stock` ka value 1 se decrease karo**.  

---

## 🛠️ **Using Parameters in Scripts**  

Agar tum **dynamic value se update karna chahte ho**, to **parameters pass kar sakte ho**.  

🔹 **Maan lo kisi ne ek saath 4 items kharid liye, to `in_stock` me se `-4` karna hai.**  

✅ **Query:**  
```json
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock -= params.amount",
    "params": {
      "amount": 4
    }
  }
}
```

✅ **Response:**  
```json
{
  "_index": "products",
  "_id": "100",
  "result": "updated"
}
```

👆 `"params": { "amount": 4 }` se **dynamic value set kar rahe hain**.  
👆 `"ctx._source.in_stock -= params.amount"` se **in_stock se 4 minus ho raha hai**.  

---

## 🚀 **Conditional Updates with `noop`**  

🔹 **Maan lo hume `in_stock` ko sirf tab decrease karna hai jab wo `0` se zyada ho.**  
🔹 **Agar `in_stock == 0` ho, to update na ho (i.e., `noop`).**  

✅ **Query:**  
```json
POST /products/_update/100
{
  "script": {
    "source": "if (ctx._source.in_stock > 0) { ctx._source.in_stock -= 1; } else { ctx.op = 'noop'; }"
  }
}
```

✅ **Agar `in_stock > 0` hoga, to `-1` ho jayega.**  
✅ **Agar `in_stock == 0` hoga, to `"result": "noop"` return hoga.**  

👆 `"ctx.op = 'noop'"` ka matlab **agar condition satisfy nahi hoti, to update na ho**.  

---

## 🗑️ **Deleting a Document Using a Script**  

🔹 **Agar kisi product ka `in_stock` `1` hai, aur wo bik gaya, to hum usse delete kar sakte hain.**  

✅ **Query:**  
```json
POST /products/_update/100
{
  "script": {
    "source": "if (ctx._source.in_stock <= 1) { ctx.op = 'delete'; } else { ctx._source.in_stock -= 1; }"
  }
}
```

✅ **Agar `in_stock > 1` hai, to `-1` ho jayega.**  
✅ **Agar `in_stock == 1` hai, to document delete ho jayega.**  

👆 `"ctx.op = 'delete'"` ka matlab **document delete ho jayega**.  

---

## 🎯 **Best Practices**  

✔️ **Frequent scripted updates avoid karo** kyunki Elasticsearch me **document immutable hote hain**, aur har update me **reindexing hoti hai**.  
✔️ **Agar simple update hai, to normal Update API use karo**.  
✔️ **Agar multiple fields update karni ho, to ek hi request me sab karo**.  
✔️ **Dynamic values ke liye `params` use karo**, taaki query reusable ho.  
✔️ **Condition check karne ke liye `ctx.op = 'noop'` ka use karo**, taaki unnecessary updates na ho.  
✔️ **Delete operation carefully use karo**, taaki galti se documents delete na ho jayein.  

---

