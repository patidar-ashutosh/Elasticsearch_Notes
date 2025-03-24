### 🔥 Boosting Query in Elasticsearch 🚀  

Boosting Query ek powerful technique hai jo relevance scoring ko manipulate karne ke liye use hoti hai. Bool Query me hum `should` clauses ka use karke relevance boost kar sakte hain, lekin agar hume kisi document ki relevance kam karni ho toh? Yahi kaam **Boosting Query** karta hai! 🎯  

## 📌 Table of Contents  

1. 🔍 [**Introduction** – Boosting Query kya hai?](#1)  
2. ⚡ [**Positive vs Negative Queries**](#2)  
3. 🎛 [**Negative Boost Parameter**](#3)  
4. 🛠 [**Boosting Query Example** – Apple Juice Filtering](#4)  
5. 🏗 [**Advanced Use Cases** – Preferences vs Requirements](#5)  
6. 🎯 [**Performance Considerations**](#6)  
7. 🏁 [**Conclusion**](#7)  

---  

## 🔍 1. Introduction – Boosting Query Kya Hai?  <a id="1"></a>

Boosting Query ka kaam hai kuch specific documents ki **relevance score ko kam karna** bina unhe completely filter kiye. Yeh Bool Query ka ek opposite behavior dikhata hai, jisme hum `should` clauses se relevance badhate hain.  

🚀 **Use Case:**  
- Aap kisi product search me dekhna chahte ho "Juice" ke results, **lekin** aapko "Apple Juice" pasand nahi hai.  
- Aap kisi recipe dataset me **Bacon** ko kam relevance dena chahte ho lekin fir bhi results me dikhana chahte ho.  

📌 **Key Points:**  
✅ **Bool Query** me hum documents ko boost kar sakte hain, lekin penalize nahi kar sakte.  
✅ **Boosting Query** me hum kisi document ki relevance score ko reduce kar sakte hain bina usse completely hataye.  
✅ **Negative Boosting** me query ka ek **positive** aur ek **negative** part hota hai.  

---  

## ⚡ 2. Positive vs Negative Queries  <a id="2"></a>

Boosting Query me do major parts hote hain:  

| Parameter | Description | Example |
|-----------|------------|---------|
| **Positive Query** ✅ | Jo documents match karega aur unka score unchanged rahega. | `"Juice"` search karna |
| **Negative Query** ❌ | Jo documents ka score reduce karega. | `"Apple Juice"` ko kam relevant banana |
| **Negative Boost** 🎛 | Score kitna reduce hoga (0 - 1.0) | `0.5` ka negative boost |  

**Diagram Representation:**  
```
🔍 Juice Query → ✅ Positive Match (Score Normal)
                     ⬇
                 ❌ Negative Query: "Apple"
                     ⬇
                🔽 Score Halved (negative_boost = 0.5)
```  

---  

## 🎛 3. Negative Boost Parameter  <a id="3"></a>

**Negative Boost** ek **floating point number** hota hai jo **0 se 1** ke beech me rehta hai.  
👉 Jitna chhota number hoga, utni zyada penalty milegi.  

🛠 **Example:**  
- Agar **negative_boost = `0.5`** hai toh documents ka score **aadha ho jayega**.  
- Agar **negative_boost = `0.2`** hai toh **zyada penalty milegi**, aur wo documents aur neeche chale jayenge.  

---  

## 🛠 4. Boosting Query Example – Apple Juice Filtering 🍏🍹  <a id="4"></a>

```json
{
  "query": {
    "boosting": {
      "positive": {
        "match": { "name": "juice" }
      },
      "negative": {
        "match": { "name": "apple" }
      },
      "negative_boost": 0.5
    }
  }
}
```  

### ✅ **Expected Output:**  
| Product | Score (Before) | Score (After) |
|---------|--------------|--------------|
| Orange Juice | `2.1` | `2.1` (No Change) |
| Grape Juice | `1.8` | `1.8` (No Change) |
| Apple Juice | `2.0` | `1.0` (Reduced) |

📌 Apple Juice ka score **aadha ho gaya**, lekin wo results me abhi bhi available hai.  

---

## 🏗 5. Advanced Use Cases – Preferences vs Requirements  <a id="5"></a>

Kabhi kabhi hume ek saath multiple preferences apply karni hoti hain jaise:  
✅ Pasta Recipes ko boost karna 🍝  
❌ Bacon Recipes ki relevance kam karna 🥓  

### 🛠 **Bool Query ke saath Boosting Query ka use**  
```json
{
  "query": {
    "boosting": {
      "positive": {
        "bool": {
          "should": [
            { "match": { "ingredients": "pasta" } }
          ]
        }
      },
      "negative": {
        "match": { "ingredients": "bacon" }
      },
      "negative_boost": 0.5
    }
  }
}
```

📌 Yahan humne **Pasta Recipes ka score boost** kiya aur **Bacon Recipes ka score kam kiya**.   

---  

## 🎯 6. Performance Considerations  <a id="6"></a>

✅ **Performance Optimization:** Agar aapko sirf documents ko **filter** karna hai toh **Boosting Query** mat use karo, balki `must_not` + `filter` approach lo.  
✅ **Caching Benefits:** Boosting Query **score calculation karta hai**, toh agar aapko fast execution chahiye toh filter-based approach better hai.  

| Scenario | Best Approach |
|----------|--------------|
| Apple Juice ko low relevance dena | Boosting Query ✅ |
| Apple Juice ko completely hata dena | Bool Query + `must_not` ❌ |
| Structured Data Filtering | Filter Query (Faster) 🚀 |

---

## 🏁 7. Conclusion  <a id="7"></a>

🔥 **Boosting Query** ek **powerful tool** hai jo aapko query results ki **priority manipulate** karne me madad karta hai.  
🔹 Agar aapko sirf kuch documents ka **score reduce** karna hai, toh Boosting Query best hai.  
🔹 Agar aapko documents **exclude** karne hai, toh **Bool Query** ka `must_not` use karo.  

💡 **Best Practice:**  
✔️ Jab bhi **relevance tuning** zaroori ho, tabhi **Boosting Query** ka use karo.  
✔️ Agar sirf filtering karni ho, toh `filter` clauses use karo performance optimize karne ke liye.  

🚀 **Next Steps:**  
- **Aggregations** me filtering kaise hoti hai?  
- **Advanced Ranking Techniques** Elasticsearch me kaise implement karein?  

