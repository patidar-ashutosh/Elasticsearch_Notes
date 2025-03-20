## 📌 **Nested Fields Limitations in Elasticsearch**  

### 📖 **Table of Contents**  
1️⃣ [Introduction](#introduction)  
2️⃣ [Nested Fields ka Performance Impact](#nested-fields-ka-performance-impact)  
3️⃣ [Nested Fields Queries](#nested-fields-queries)  
4️⃣ [Nested Fields ke Limits](#nested-fields-ke-limits)  
5️⃣ [Better Design Considerations](#better-design-considerations)  
6️⃣ [Conclusion](#conclusion)  

---

### 🏁 **Introduction**  
Elasticsearch me **nested fields** kaafi useful hote hain jab hume **arrays of objects** ko query karna hota hai.  
Lekin inka **proper use** karna zaroori hai, kyunki ye **performance aur scaling issues** la sakte hain.  

---

### 🚀 **Nested Fields ka Performance Impact**  
✅ **Nested Fields se Document Count Badhta Hai**  
- Elasticsearch **har nested object** ke liye ek **Lucene document** create karta hai.  
- Agar **1 million documents** index karein, jisme **10 nested objects** ho, to Elasticsearch actually **11 million documents** store karega.  
- Ye extra documents indexing time aur resources **jada consume** karenge.  

✅ **Scale Badne Par Performance Impact**  
- Elasticsearch fast hai, but agar **nested objects** bahut zyada ho gaye, to queries **slow ho sakti hain**.  
- Jitna jada nested data hoga, utna zyada **performance degrade** hone ka chance hoga.  

✅ **Elasticsearch ke Safeguards**  
- Ye issues avoid karne ke liye Elasticsearch me **built-in limits** hote hain jo excessive memory usage ko **prevent** karte hain.  

---

### 🔍 **Nested Fields Queries**  
- **Nested objects** ko query karne ke liye **specialized queries** lagti hain.  
- Standard queries se **different syntax** hoti hai.  
- **Nested query** kaafi flexible hai, but queries **thodi complex** ho sakti hain.  

---

### 📏 **Nested Fields ke Limits**  
🚨 **Index ke andar Nested Fields ki Limit**  
- **Default: 50 nested fields per index**  
- Isko increase kar sakte ho, but agar **jada nested fields** chahiye, to shayad tumhari document mapping **galat ho sakti hai**.  

🚨 **Ek Document ke andar Maximum Nested Objects**  
- **Default: 10,000 nested objects per document**  
- Ye limit memory-related issues se **bachne ke liye hai**.  
- Isko increase kar sakte ho, but **recommend nahi kiya jata**.  

---

### 🔄 **Better Design Considerations**  
💡 **Example: E-commerce Webshop (Shopify-type Service)**  
🔹 **Bad Design (Bahut Zyada Nested Fields)**  
- Agar ek **webshop document** ke andar **saare orders** nested field me store kar diye, to problem ho sakti hai.  
- Har order ke andar **multiple order lines** hongi, to document jaldi **bada ho jayega**.  
- **10,000 nested objects** ki limit **cross ho sakti hai**.  

🔹 **Better Design (Data ko Alag Indices me Store Karna)**  
- **Webshops aur Orders ke alag indices banao**  
  - **Webshops index** me sirf basic shop details ho.  
  - **Orders index** me orders aur unke **order_lines field** ho.  
- Isse **nested objects limit ka issue nahi hoga** aur performance bhi better rahegi.  

**⚠️ Key Takeaway:**  
Agar ek document me **hazaron nested objects** ho sakte hain, to **better mapping strategies** ka socho.  

---

### 🏁 **Conclusion**  
✔️ **Nested fields** powerful hain, but unke **performance aur scaling issues** bhi hain.  
✔️ **Nested fields ka limit samajhna zaroori hai** taki memory aur performance issues na aayein.  
✔️ Agar document **bada hone ka chance** hai, to **better design choices** adopt karo.  

⚡ **In limitations ko dhyan me rakho, aur Elasticsearch ko efficiently use karo! 🚀**