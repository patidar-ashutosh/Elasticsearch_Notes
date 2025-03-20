# 📌 **Understanding Routing in Elasticsearch**  

## 📜 **Table of Contents**  
1. 🔹 [Introduction](#introduction)  
2. ⚙️ [What is Routing?](#what-is-routing)  
3. 🔢 [Routing Formula](#routing-formula)  
4. 🚀 [How Elasticsearch Uses Routing](#how-elasticsearch-uses-routing)  
5. 🔄 [Custom Routing](#custom-routing)  
6. ❌ [Why Can't We Change the Number of Shards?](#why-cant-we-change-the-number-of-shards)  
7. 🎯 [Key Takeaways](#key-takeaways)  

---

## 🔹 **Introduction**  
Tum ab tak **documents ko index, update, delete aur retrieve karna seekh chuke ho**.  
Ab ek **important internal concept** dekhte hain – **Routing**!  

🤔 **Sawal ye uthta hai:**  
- Elasticsearch kaise decide karta hai ki ek document **kaunse shard** pe store hoga?  
- Jab tum kisi document ko retrieve/update/delete karte ho, Elasticsearch **kaise turant dhoondh leta hai?**  

🔍 **Answer hai – Routing!**  

---

## ⚙️ **What is Routing?**  
🔹 **Routing ka kaam hai correct shard ka determination karna**  
✔️ **Indexing ke waqt:** Kaunsa shard document store karega?  
✔️ **Retrieving ke waqt:** Kaunsa shard se document uthana hai?  

Elasticsearch me **routing** ka use hota hai data ko **specific shards** me store aur retrieve karne ke liye. Default behavior me Elasticsearch **document ID ke hash** ko use karke decide karta hai ki document kis shard me jayega.  

👉 **Par agar hume apna custom routing set karna ho to hum `routing` parameter ka use kar sakte hain.**  

### **1️⃣ Default Routing:**  
- By default, Elasticsearch ek **formula** ka use karke **document ke ID se shard determine** karta hai.  
- Agar tum **document retrieve/update/delete kar rahe ho, to wahi formula apply hota hai**.  

Agar hum **custom routing nahi dete**, to Elasticsearch **document ID ka hash** calculate karke **shard decide** karega.  

```json
POST /products/_doc/1
{
    "name": "iPhone 15",
    "category": "mobile"
}
```
🔹 **Yaha Elasticsearch apne hisaab se shard decide karega.**  

---

### **2️⃣ Custom Routing (Manual)**
Agar hum chahte hain ki **same category wale products ek hi shard me jaye**, to hum **routing parameter** specify kar sakte hain:  

```json
POST /products/_doc/2?routing=mobile
{
    "name": "Samsung Galaxy S23",
    "category": "mobile"
}
```
🔹 **Ab saare `mobile` category wale products ek hi shard me store honge.**  
🔹 **Yadi `category = "laptop"` hoti to wo alag shard me jata.**  

```json
GET /products/_doc/2?routing=mobile
```
🔹 **Ye query sirf `mobile` category wale shard pe jayegi, pura cluster scan nahi karegi.**  
🔹 **Faster Search 🚀 kyunki unnecessary shards query nahi hote.**  

---

## 🔢 **Routing Formula**  
Elasticsearch ek **simple formula** use karta hai:  

\[
shard = hash(_routing) \mod \text{number of shards}
\]

🚀 **_routing kya hota hai?**  
- **Default:** _routing = `_id` (matlab document ID)  
- **Custom:** Tum apni marzi se custom routing value specify kar sakte ho (advanced use cases ke liye)  

**📌 Example:**  
Maan lo ek index hai **products** jisme **5 primary shards** hain.  

Agar hum ek document insert karte hain:  
```json
POST /products/_doc/1
{
    "name": "iPhone 15"
}
```
To Elasticsearch ye calculate karega:  
```plaintext
shard = hash(1) % 5
```
Agar `hash(1) = 123456789` aaya to:  
```plaintext
shard = 123456789 % 5 = 4
```
To ye document **shard-4** me store ho jayega.  

---

## 🚀 **How Elasticsearch Uses Routing?**  

1️⃣ **Indexing ke waqt**  
   - Jab tum ek **naya document store karte ho**, routing formula decide karta hai ki **kaunsa shard use hoga**.  

2️⃣ **Retrieve, Update, Delete ke waqt**  
   - Jab tum **document ID specify karke retrieve/update/delete karte ho**, Elasticsearch **formula apply karke correct shard pe request bhejta hai**.  

👀 **Example:**  
Agar tum request bhejte ho:  
```json
GET /products/_doc/105
```
To Elasticsearch:  
✔️ Pehle formula use karega **shard determine karne ke liye**  
✔️ Fir **directly usi shard pe query execute karega**  

⚡ **Is wajah se document retrieval kaafi fast hoti hai!**  

---

## 🔄 **Custom Routing**  

By default, Elasticsearch document ID ka hash leke shard determine karta hai.  
🔹 **Lekin tum chaho to custom `_routing` value bhi specify kar sakte ho.**  

✅ **Example:** Custom routing value `"electronics"` use karke document index karna  
```json
PUT /products/_doc/105?routing=electronics
{
  "name": "Laptop",
  "price": 50000
}
```
✔️ **Yeh query `electronics` ka hash nikalke correct shard decide karegi.**  

✅ **Retrieve the document with custom routing**  
```json
GET /products/_doc/105?routing=electronics
```
⚠️ **Agar tum routing value na do, to Elasticsearch document nahi dhoondh paayega!**  

💡 **Custom Routing ka use kab karein?**  
✔️ Jab tum **specific categories ya tenants ke documents ek hi shard pe store karna chahte ho**  
✔️ **Search performance optimize karna ho** by reducing the number of shards checked  

---

## ❌ **Why Can't We Change the Number of Shards?**  

🤔 **Elasticsearch me ek baar index create hone ke baad tum number of shards badal nahi sakte. Kyu?**  

🚀 **Reason:** Routing formula  
- **Formula me number of shards fixed hoti hai**  
- Agar tum **number of shards change karoge, to pehle indexed documents wrong shard pe point karenge!**  

### **📌 Example:**
Maan lo humne ek index banaya jisme **5 primary shards** hain:
```json
PUT /products
{
    "settings": {
        "number_of_shards": 5,
        "number_of_replicas": 1
    }
}
```
Aur hum ek document insert karte hain:
```json
POST /products/_doc/1
{
    "name": "MacBook Pro"
}
```
#### **📌 Shard Selection Calculation**
```plaintext
shard = hash(1) % 5
shard = 123456789 % 5 = 4
```
To **ye document `shard-4` me jayega**.  

---

### **📌 Kya Hoga Agar Hum Shards Change Kar Dein?**  
Agar hum **number_of_shards = 6** kar dete hain, to **formula change ho jayega**:
```plaintext
shard = hash(1) % 6
shard = 123456789 % 6 = 3  ❌
```
Matlab **document ab shard-3 me expect hoga**, par **actual me wo shard-4 me pada hoga**, to **Elasticsearch ko wo milega hi nahi!** 😱  

---

🚀 **Solution:**  
🔹 **Primary shards ko create hone ke baad change nahi kar sakte.**  
🔹 Agar shards badalne ka zaroori ho to **Reindex API** ka use karke naye index me data migrate karna padega. [Learn More About Reindex API](./reindex_api.md)  
🔹 **Number of replicas** badal sakte ho, kyuki wo data ko affect nahi karta.  
✔️ Elasticsearch `shrink` aur `split` APIs provide karta hai jisse ye process easy ho jata hai.  

---

## 🎯 **Key Takeaways**  

✅ **Routing ka kaam correct shard ka determination karna hota hai**  
✅ **Default routing me `_id` ka hash lekar shard determine hota hai**  
✅ **Jab tum retrieve/update/delete karte ho, Elasticsearch wahi formula use karke direct correct shard pe request bhejta hai**  
✅ **Custom routing ka use kiya ja sakta hai agar tumhe documents specific shards pe store karne ho**  
✅ **Shards ki count change nahi kar sakte, warna document retrieval issue ho sakta hai**  

---

---
[Reference Video Link](https://youtu.be/hP6i_lh3A60?si=y0jxR52rmwPBkJZy) 
---