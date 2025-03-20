# 📌 Join Limitations

## 📖 Table of Contents  

1️⃣ **🔗 Join Fields Recap**  
2️⃣ **🚫 Joins Across Indexes are NOT Allowed**  
3️⃣ **🔄 Parent & Child Documents Must Be on the Same Shard**  
4️⃣ **📌 Only One Join Field Per Index**  
5️⃣ **👨‍👩‍👦 One Parent, Multiple Children Rule**  
6️⃣ **⚡ Why These Limitations Exist? (Performance Reasons)**  
7️⃣ **🔚 Summary & Key Takeaways**  

---

## 1️⃣ 🔗 Join Fields Recap  

Elasticsearch me **parent-child relationships** banane ke liye **join field** ka use hota hai.  
- **Parent Document:** Yeh ek root-level entity hoti hai (jaise Department).  
- **Child Document:** Yeh parent se linked hota hai (jaise Employees).  
- **Joining Queries:** Parent aur child documents ko link karke queries chalane ke liye use hoti hain.  

Agar basic join queries ka idea nahi hai, to pehle isko samajhna zaroori hai! 🚀  

---

## 2️⃣ 🚫 Joins Across Indexes are NOT Allowed  

**❌ Elasticsearch allow nahi karta ki hum alag-alag indexes ke documents ko join karein.**  

### **Example (Not Allowed)**
🚫 Agar hum `departments` alag index me aur `employees` kisi doosre index me store karein, to hum inhe **join nahi kar sakte!**  

```bash
departments_index 🚫 employees_index
```

### **✔️ Allowed Approach**
✅ **Dono ko ek hi index me store karna hoga** with a **join field.**  

```bash
company_index
  ├── department (parent)
  ├── employee (child)
```

### **🚀 Why?**
- **Joins bahut slow ho jate hain agar alag-alag indexes pe kiye jayein.**  
- **Traditional databases (SQL) ki tarah Elasticsearch JOINs ko optimize nahi karta.**  
- **Is limitation ka goal performance optimize karna hai.**  

---

## 3️⃣ 🔄 Parent & Child Documents Must Be on the Same Shard  

✅ **Parent aur child documents ko same shard pe store karna compulsory hai.**  

### **⚠️ Kya Problem Hoti Hai?**  
Elasticsearch sharding use karta hai, aur agar parent-child documents alag-alag shards pe chale gaye, to **join queries fail ho sakti hain ya slow ho sakti hain.**  

### **🔧 Solution: Use Routing Parameter**  
Jab bhi **child document add karein, hamesha `routing` parameter specify karein** taaki woh **parent ke shard pe hi jaye.**  

```json
PUT company/_doc/1?refresh
{
  "name": "IT Department",
  "join_field": "department"
}

PUT company/_doc/2?routing=1&refresh
{
  "name": "John Doe",
  "join_field": { "name": "employee", "parent": "1" }
}
```

👉 **Yeh ensure karega ki employee (`id:2`) IT Department (`id:1`) ke shard pe hi store ho.**  

---

## 4️⃣ 📌 Only One Join Field Per Index  

**⚠️ Elasticsearch ek index me sirf ek join field allow karta hai.**  
- **Ye SQL relational databases se different hota hai.**  
- **Hum ek join field me multiple relationships define kar sakte hain.**  

### **✔️ Allowed Approach:**  
```json
"join_field": {
  "type": "join",
  "relations": {
    "department": "employee",
    "manager": "staff"
  }
}
```
👉 **Yeh ek hi join field ke andar multiple relationships handle kar lega.**  

---

## 5️⃣ 👨‍👩‍👦 One Parent, Multiple Children Rule  

✅ **Ek parent multiple children hold kar sakta hai, par ek child sirf ek hi parent ka hissa ho sakta hai.**  

### **✔️ Allowed Structure**
```bash
IT Department (parent)
  ├── John Doe (child)
  ├── Alice (child)
```

🚫 **John Doe ek saath multiple departments ka child nahi ho sakta.**  

Agar tumhe **multi-parent relationships** chahiye, to **nested fields ya denormalization** use karo.

---

## 6️⃣ ⚡ Why These Limitations Exist? (Performance Reasons)  

Yeh saari limitations **Elasticsearch ke high-speed search architecture** ko maintain karne ke liye hai.  

🔹 **Distributed Nature:** Elasticsearch alag-alag nodes aur shards pe distributed hota hai. Agar joins allowed kiye jayein, to cross-node queries slow ho sakti hain.  

🔹 **Avoiding Complex Queries:** Agar **parent-child relationships bahut zyada ho jayein, to query execution slow ho sakta hai.**  

🔹 **Routing Optimization:** Routing ensure karta hai ki **queries efficiently execute ho.**  

---

## 7️⃣ 🔚 Summary & Key Takeaways  

✅ **Joins sirf ek hi index ke andar ho sakte hain.**  
✅ **Parent aur child ko same shard pe store karna zaroori hai (routing required).**  
✅ **Ek index me sirf ek hi join field ho sakti hai, par uske andar multiple relationships ho sakti hain.**  
✅ **Ek child sirf ek parent ka hissa ho sakta hai, but ek parent ke multiple children ho sakte hain.**  
✅ **Ye limitations mainly performance ko optimize karne ke liye hain.**  

---

