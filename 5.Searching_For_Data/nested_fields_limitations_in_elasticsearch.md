## 📌 **Nested Fields Limitations in Elasticsearch**  

## 📖 **Table of Contents**  

1️⃣ [Introduction](#1)  
2️⃣ [Nested Fields ka Performance Impact](#2)  
3️⃣ [Nested Fields Queries](#3)  
4️⃣ [Nested Fields ke Limits](#4)  
5️⃣ [Better Design Considerations](#5)  
6️⃣ [Conclusion](#6)  

---

## 1. 🏁 **Introduction**  <a id="1"></a>

Elasticsearch me **nested fields** kaafi useful hote hain jab hume **arrays of objects** ko query karna hota hai.  
Lekin inka **proper use** karna zaroori hai, kyunki ye **performance aur scaling issues** la sakte hain.  

---

## 2. 🚀 **Nested Fields ka Performance Impact**  <a id="2"></a>

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

## 3. 🔍 **Nested Fields Queries**  <a id="3"></a>

- **Nested objects** ko query karne ke liye **specialized queries** lagti hain.  
- Standard queries se **different syntax** hoti hai.  
- **Nested query** kaafi flexible hai, but queries **thodi complex** ho sakti hain.  

---

## 4. 📏 **Nested Fields ke Limits**  <a id="4"></a>

🚨 **Index ke andar Nested Fields ki Limit**  
- **Default: 50 nested fields per index**  
- Isko increase kar sakte ho, but agar **jada nested fields** chahiye, to shayad tumhari document mapping **galat ho sakti hai**.  

🚨 **Ek Document ke andar Maximum Nested Objects**  
- **Default: 10,000 nested objects per document**  
- Ye limit memory-related issues se **bachne ke liye hai**.  
- Isko increase kar sakte ho, but **recommend nahi kiya jata**.  

---

## 5. 🔄 **Better Design Considerations**  <a id="5"></a>

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

## 6. 🏁 **Conclusion**  <a id="6"></a>

✔️ **Nested fields** powerful hain, but unke **performance aur scaling issues** bhi hain.  
✔️ **Nested fields ka limit samajhna zaroori hai** taki memory aur performance issues na aayein.  
✔️ Agar document **bada hone ka chance** hai, to **better design choices** adopt karo.  

⚡ **In limitations ko dhyan me rakho, aur Elasticsearch ko efficiently use karo! 🚀**