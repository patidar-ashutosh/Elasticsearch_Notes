## **🔹 Optimistic Concurrency Control (OCC) in Elasticsearch**  

### **📜 Table of Contents**  
1️⃣ [📌 What is Optimistic Concurrency Control (OCC)?](#1)  
2️⃣ [🔹 Why Use Optimistic Concurrency Control?](#2)  
3️⃣ [🚀 Scenario Without OCC (Data Loss Problem)](#3)  
4️⃣ [🚀 Scenario With OCC (No Data Loss)](#4)  
5️⃣ [🔹 How OCC Works in Elasticsearch?](#5)  
6️⃣ [🔹 Example: Using OCC in Elasticsearch](#6)  
7️⃣ [🔹 Benefits of Optimistic Concurrency Control](#7)  
8️⃣ [🔹 Conclusion](#8)  

---

### **📌 What is Optimistic Concurrency Control (OCC)?**  <a id="1"></a>
Optimistic Concurrency Control (**OCC**) ek **strategy hai jisme multiple clients ek hi document ko bina lock kiye access aur update kar sakte hain**, lekin **Elasticsearch ensure karta hai ki sirf latest version hi update ho**.  

💡 **Matlab:** Agar ek document **do alag-alag clients ke paas same time par ho**, aur dono update karna chahein, to **sirf wahi update accept hoga jo latest version ke upar apply hoga**.  

---

### **🔹 Why Use Optimistic Concurrency Control?** <a id="2"></a>
✔ **Data Overwrite Issues ko prevent karta hai**  
✔ **Multiple users ke sath consistency maintain karta hai**  
✔ **Performance better hoti hai, kyunki locks ka use nahi hota**  
✔ **Distributed systems me conflicts avoid karne ke liye useful hai**  

---

### **🚀 Scenario Without OCC (Data Loss Problem)** <a id="3"></a>
1️⃣ **Client A** balance update karna chahta hai:  
   - Wo document ko fetch karta hai (`balance: 1000`)  
   - **200 rs withdraw karta hai** (`balance: 800`)  
   - **Elasticsearch me save karta hai**  

2️⃣ **Client B** bhi balance update karna chahta hai:  
   - Wo bhi document ko **same time pe fetch karta hai (`balance: 1000`)**  
   - **500 rs withdraw karta hai** (`balance: 500`)  
   - **Elasticsearch me save karta hai**  

📌 **Result:** **Client A ka update overwrite ho jata hai**, aur **final balance galat ho jata hai (500 instead of 800)**.  
⛔ **Yeh ek critical bug hai, jo OCC ke bina ho sakta hai!**  

---

### **🚀 Scenario With OCC (No Data Loss)** <a id="4"></a>
✔ **Elasticsearch `_version` field use karke ensure karta hai ki koi old update mistakenly overwrite na kare.**  

1️⃣ **Client A fetches document (Version 1)**  
   - `balance = 1000`, `_version = 1`  
   - Withdraw **200** → New balance **800**  
   - Elasticsearch me update request send karta hai:  
   ```http
   PUT /bank/_doc/12345?if_seq_no=1&if_primary_term=1
   {
     "balance": 800
   }
   ```  
   - **Update successful**, `_version = 2` ho jata hai  

2️⃣ **Client B fetches document (Version 1)**  
   - `balance = 1000`, `_version = 1`  
   - Withdraw **500** → New balance **500**  
   - Update request send karta hai:  
   ```http
   PUT /bank/_doc/12345?if_seq_no=1&if_primary_term=1
   {
     "balance": 500
   }
   ```  
   - **Elasticsearch Rejects This Update!** ❌  
   - Kyunki **version already change ho chuka hai (`_version = 2`)**  
   - Client B ko **conflict error milega**, aur **wo document dubara fetch karke latest balance ke basis pe update karega**  

---

### **🔹 How OCC Works in Elasticsearch?** <a id="5"></a>
1️⃣ **Client document fetch karta hai (`_version` ke sath)**  
2️⃣ **Client update request bhejta hai, lekin condition add karta hai (`if_seq_no` & `if_primary_term`)**  
3️⃣ **Agar document ka version match karta hai to update hota hai, warna error aata hai**  
4️⃣ **Agar error aata hai to client latest document dubara fetch karke update karta hai**  

---

### **🔹 Example: Using OCC in Elasticsearch** <a id="6"></a>
Agar tumhe ensure karna hai ki **sirf latest document update ho**, to tum **`if_seq_no` aur `if_primary_term`** ka use kar sakte ho.

```http
PUT /bank/_doc/12345?if_seq_no=1&if_primary_term=1
{
  "balance": 800
}
```
✔ Agar document ka **current `_seq_no = 1` aur `_primary_term = 1` hai**, to **update allow hoga**.  
❌ **Agar version mismatch ho gaya, to Elasticsearch conflict error dega (`409 Conflict`).**  

---

### **🔹 Benefits of Optimistic Concurrency Control** <a id="7"></a>
✅ **Data Integrity:** Multiple users ke changes kabhi overwrite nahi honge  
✅ **No Locks Needed:** Performance improve hoti hai kyunki system me locks nahi lagte  
✅ **Automatic Conflict Handling:** Agar version mismatch hota hai to error milta hai, jisse client dubara latest data fetch karke update kare  
✅ **Best for High-Performance Systems:** Banking, E-commerce, Distributed Databases me useful hai  

---

### **🔹 Conclusion** <a id="8"></a>
🔹 **Optimistic Concurrency Control ensures that no outdated updates overwrite the latest data.**  
🔹 **It prevents data loss and inconsistency in high-concurrency systems.**  
🔹 **Elasticsearch uses `_seq_no` and `_primary_term` to handle OCC efficiently.**  
🔹 **Without OCC, multiple updates can cause wrong values to be saved, leading to critical bugs.**  

💡 **Elasticsearch OCC ko default versioning se better mana jata hai, kyunki yeh multiple nodes ke beech data consistency ensure karta hai.** 🚀

---
[Reference Video Link](https://youtu.be/bCo8MZ3Ms-I?si=hHyX789QPN_I1rDT)
---

