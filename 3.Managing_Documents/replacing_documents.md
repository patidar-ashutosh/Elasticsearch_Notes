# 📜 **Replacing Documents in Elasticsearch**  

## 📌 **Table of Contents**  
1. 🔹 [Introduction](#introduction)  
2. ⚡ [What is Document Replacement?](#what-is-document-replacement)  
3. 📝 [Basic Example of Replacing a Document](#basic-example-of-replacing-a-document)  
4. 🛠️ [How Replacement Works](#how-replacement-works)  
5. 🚀 [Key Considerations](#key-considerations)  
6. 🎯 [Best Practices](#best-practices)  

---

## 🔹 **Introduction**  
Ab tak tum **naye documents ko index karna aur existing documents ko update karna** seekh chuke ho.  

Ab **documents ko replace karna sikhenge**.  

✔️ **Ye process kaafi simple hai** aur **humne isse pehle bhi use kiya hai**.  
✔️ **Jo query hum new document index karne ke liye use karte hain, wahi replace karne ke liye bhi kaam aati hai**.  

---

## ⚡ **What is Document Replacement?**  

🔹 **Document replace karne ka matlab:**  
✔️ **Existing document ko puri tarah se overwrite karna**  
✔️ **Jo fields naye document me nahi hain, wo delete ho jayengi**  
✔️ **Update se different hai, kyunki update sirf kuch fields change karta hai**  

🔹 **Kaha useful hota hai?**  
✔️ **Agar tumhe pura document modify karna hai**  
✔️ **Agar tum sirf ek hi query me naye aur purane fields ko replace karna chahte ho**  

---

## 📝 **Basic Example of Replacing a Document**  

Maan lo tumhare paas **`id: 100`** ka ek product document hai:  

✅ **Current document:**  
```json
{
  "name": "Toaster",
  "price": 20,
  "in_stock": 5,
  "tags": ["electronics", "kitchen"]
}
```

Ab **tum price change karna chahte ho** aur **document replace karna chahte ho**.  

✅ **Replace document query:**  
```json
PUT /products/_doc/100
{
  "name": "Toaster",
  "price": 25,
  "in_stock": 5
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

👆 `"tags"` field **automatically delete ho gayi** kyunki humne **sirf `"name"`, `"price"` aur `"in_stock"` rakha hai**.  

---

## 🛠️ **How Replacement Works?**  

🔹 **Jab tum `PUT /index/_doc/{id}` use karte ho:**  
✔️ **Agar document exist nahi karta, to naya document create ho jata hai**  
✔️ **Agar document exist karta hai, to pura replace ho jata hai**  
✔️ **Koi bhi missing fields delete ho jati hain**  

🔹 **Difference between Update vs Replace:**  

| Feature | Update (`POST _update`) | Replace (`PUT _doc/{id}`) |
|---------|----------------|----------------|
| **Existing Fields Remain?** | ✔️ Haan | ❌ Nahi |
| **Missing Fields Delete Hote Hain?** | ❌ Nahi | ✔️ Haan |
| **Kaunsi Fields Modify Hoti Hain?** | Sirf specified fields | Pura document |
| **Use Case** | Jab sirf kuch fields change karni ho | Jab pura document overwrite karna ho |

---

## 🚀 **Key Considerations**  

🔹 **Kya replace karne par version change hota hai?**  
✔️ Haan, Elasticsearch har replace operation me **document ka `_version` increment kar deta hai**.  

🔹 **Kya replace operation slow hota hai?**  
✔️ Haan, kyunki Elasticsearch internally **pura document delete karke naye wale ko insert karta hai**.  

🔹 **Kya replace operation automatic hota hai?**  
✔️ Nahi, tumhe manually replace query likhni padti hai.  

---

## 🎯 **Best Practices**  

✔️ **Agar sirf kuch fields update karni hain, to `POST _update` ka use karo, `PUT _doc` nahi**.  
✔️ **Agar document ko completely replace kar rahe ho, to ensure karo ki naye document me saare required fields ho**.  
✔️ **Frequent replacements avoid karo, kyunki ye costly operation hota hai**.  
✔️ **Agar existing fields retain rakhni hain, to `GET` query karke unhe include karo**.  

---

