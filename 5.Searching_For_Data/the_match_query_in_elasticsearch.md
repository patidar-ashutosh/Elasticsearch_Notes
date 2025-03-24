# The Match Query in Elasticsearch

## 📌 Introduction
Match query ek full-text search query hai jo specified terms ko match karne ke liye use hoti hai. Ye Elasticsearch me sabse widely used query hai kyunki ye structured aur unstructured data dono ke liye kaafi effective hai.

## 📑 Table of Contents  

1️⃣ [🔍 **Match Query Kya Hai?**](#1)  
2️⃣ [🎯 **Query Analysis ka Role**](#2)  
3️⃣ [📝 **Basic Syntax aur Example**](#3)  
4️⃣ [⚙️ **Operator Parameter: OR vs AND**](#4)  
5️⃣ [📊 **Multiple Word Search ka Mechanism**](#5)  
6️⃣ [📅 **Numbers, Dates aur Boolean Values ke saath Usage**](#6)  
7️⃣ [🏁 **Conclusion**](#7)  

---

## 1. 🔍 Match Query Kya Hai? <a id="1"></a>

Match query ka use tab hota hai jab hume kisi field ke andar specific terms ko search karna ho. Is query me terms ko analyze kiya jata hai aur phir inverted index me lookup kiya jata hai taaki matching documents retrieve kiye ja sakein.

---

## 2. 🎯 Query Analysis ka Role <a id="2"></a>

Jab bhi hum ek match query run karte hain, Elasticsearch us query ko analyze karta hai. Analysis process me:
1. **Lowercasing** - Uppercase letters ko lowercase me convert kar diya jata hai.
2. **Tokenization** - Query ko multiple tokens me split kiya jata hai.
3. **Filtering** - Stop words ya extra characters remove kiye jate hain.

Example:
```
"PASTA" -> lowercase -> "pasta"
```
Is wajah se "PASTA" aur "pasta" ka search result same aata hai.

---

## 3. 📝 Basic Syntax aur Example <a id="3"></a>

Agar hume kisi product ke `name` field me "pasta" search karna ho to hum match query ka use karenge:

```json
{
  "query": {
    "match": {
      "name": "pasta"
    }
  }
}
```

✅ **Expected Output:** Yeh query sabhi documents ko return karegi jisme `name` field me "pasta" word ho.

---

## 4. ⚙️ Operator Parameter: OR vs AND <a id="4"></a>

By default, match query ka `operator` parameter "OR" hota hai, jiska matlab hai agar koi bhi term match hota hai to document return hoga.

Agar hume sirf wahi documents chahiye jisme **saare terms** match ho rahe hain, to hum `AND` operator use karenge:

```json
{
  "query": {
    "match": {
      "name": {
        "query": "chicken pasta",
        "operator": "AND"
      }
    }
  }
}
```

✅ **Expected Output:** Is query ka result sirf un documents tak limited hoga jisme "chicken" aur "pasta" dono words ho.

---

## 5. 📊 Multiple Word Search ka Mechanism <a id="5"></a>

Jab hum ek se zyada words search karte hain jaise "chicken pasta", to Elasticsearch unhe analyze karta hai aur dono words ke liye inverted index check karta hai.

- **Operator: OR** (default) → Agar "chicken" ya "pasta" kisi bhi document me ho to match hoga.
- **Operator: AND** → Dono words ka hona zaroori hai.

---

## 6. 📅 Numbers, Dates aur Boolean Values ke saath Usage <a id="6"></a>

Match query sirf text fields tak limited nahi hai. Iska use hum **numbers, dates aur boolean values** ke saath bhi kar sakte hain.

```json
{
  "query": {
    "match": {
      "price": 100
    }
  }
}
```

✅ **Expected Output:** Yeh query sabhi products ko return karegi jinka `price` 100 hai.

⚠️ **Note:** Agar hum user input pe dependent nahi hain to numbers, dates aur booleans ke liye term level queries ka use zyada better hota hai.

---

## 7. 🏁 Conclusion <a id="7"></a>

- **Match query** ka use full-text search ke liye hota hai.
- Query ko analyze karke inverted index me matching results fetch kiye jate hain.
- `operator` parameter ke through OR/AND ka behavior control kiya ja sakta hai.
- Numbers, dates aur boolean values ke saath bhi ye query kaam karti hai.

