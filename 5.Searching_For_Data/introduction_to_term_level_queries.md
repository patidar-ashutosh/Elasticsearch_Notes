# 📌 **Table of Contents**
1️⃣ **Introduction to Term Level Queries** 🧐  
2️⃣ **Key Characteristics of Term Level Queries** 🔑  
3️⃣ **Term Level Query Example** 📝  
4️⃣ **Case Sensitivity & Exact Matching** 🔠  
5️⃣ **Common Mistake: Using Term Query on Text Fields** 🚨  
6️⃣ **Correct Approach: Use Keyword Fields for Term Queries** ✅  
7️⃣ **Final Recap & Learnings** 🎯  

---

## 1️⃣ **Introduction to Term Level Queries** 🧐  
Elasticsearch mein queries ke alag-alag types hote hain, aur **Term Level Queries** ka use tab hota hai jab hume **exact value** search karni ho.  

👉 **Example:**  
- Agar tum kisi e-commerce app mein products ko **brand, price, ya color** ke basis pe filter karna chahte ho, to Term Queries ka use karoge.  

⚠️ **Important Note:**  
Term Level Queries **analyzed nahi hote**, iska matlab jo bhi value hum search kar rahe hain, usko **as it is** compare kiya jata hai.  

---

## 2️⃣ **Key Characteristics of Term Level Queries** 🔑  
✔️ **Exact Value Match**: Sirf wahi documents match honge jisme value exactly same ho.  
✔️ **Case Sensitive**: `Nike` aur `nike` ko different maana jayega.  
✔️ **Substring Match Nahi Hota**: Agar tum `"Nik"` search karoge, to `"Nike"` match nahi karega.  
✔️ **Best for Structured Data**: Keywords, numbers, dates, etc.  

---

## 3️⃣ **Term Level Query Example** 📝  
Maan lo ki humare paas ek **products** index hai jisme humari documents store hain:  

### **📄 Sample Document**
```json
{
  "_index": "products",
  "_id": "1",
  "_source": {
    "brand.keyword": "Nike",
    "price": 5000
  }
}
```

### **🔍 Query to Search for Nike**
```json
GET products/_search
{
  "query": {
    "term": {
      "brand.keyword": "Nike"
    }
  }
}
```

### **📌 Expected Output**
```json
{
  "hits": {
    "total": 1,
    "hits": [
      {
        "_source": {
          "brand.keyword": "Nike",
          "price": 5000
        }
      }
    ]
  }
}
```
🟢 `"Nike"` match ho gaya kyunki `"brand.keyword"` field ek **keyword** type ka field hai, jo analyzed nahi hota.  

---

## 4️⃣ **Case Sensitivity & Exact Matching** 🔠  
### 🚨 **Wrong Query (Case Sensitive Issue)**
```json
GET products/_search
{
  "query": {
    "term": {
      "brand.keyword": "nike"
    }
  }
}
```
### ❌ Expected Output (No Match)
```json
{
  "hits": {
    "total": 0,
    "hits": []
  }
}
```
⚠️ `"nike"` match nahi karega kyunki **Term Query case-sensitive hoti hai**.  

---

## 5️⃣ **Common Mistake: Using Term Query on Text Fields** 🚨  
Agar tum kisi **text** field pe Term Query lagane ki koshish karoge, to result unexpected aayega.  

### **❌ Wrong Example (Using term on text field)**
```json
GET products/_search
{
  "query": {
    "term": {
      "brand": "Nike"
    }
  }
}
```
### ⚠️ **Problem**  
Elasticsearch **text fields ko analyze** karta hai (lowercase, tokenization, stemming, etc.), is wajah se `"Nike"` ko `"nike"` bana diya jayega, aur **query match nahi karegi**.  

---

## 6️⃣ **Correct Approach: Use Keyword Fields for Term Queries** ✅  
Agar tumhe **exact match** karna hai, to **keyword fields** ka use karo.  

✔️ **Right Approach:**  
```json
GET products/_search
{
  "query": {
    "term": {
      "brand.keyword": "Nike"
    }
  }
}
```
✅ **Yeh Query kaam karegi** kyunki `"brand.keyword"` field **analyzed nahi hai**, aur jo value store hai, wahi compare hogi.  

---

## 7️⃣ **Final Recap & Learnings** 🎯  
📌 **Term Level Queries ka use exact value match ke liye hota hai.**  
📌 **Yeh case-sensitive hote hain, aur partial match nahi hota.**  
📌 **Text fields ke sath Term Query use nahi karni chahiye.**  
📌 **Keyword fields ka use karo agar exact match karna hai.**  

---

