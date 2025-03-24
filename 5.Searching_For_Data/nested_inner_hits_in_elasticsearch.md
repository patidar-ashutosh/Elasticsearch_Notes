### 🔍 Nested Inner Hits in Elasticsearch  

Elasticsearch me **Nested Inner Hits** ek powerful feature hai jo nested queries ke results ko aur bhi informative banata hai. Jab hum kisi **nested object** pe query chalate hain, to by default hume **sirf parent document** milta hai. Lekin kabhi-kabhi hume yeh bhi jaanna hota hai ki **kaunsa nested object match** hua hai. **Inner hits** isi kaam ke liye hota hai.  

## 📌 Table of Contents  

1️⃣ **[Nested Queries Recap](#1)** 🏗️  
2️⃣ **[Inner Hits Kya Hai?](#2)** 🔎  
3️⃣ **[Inner Hits Enable Kaise Karein?](#3)** ⚙️  
4️⃣ **[Inner Hits Ka Structure](#4)** 🏛️  
5️⃣ **[Inner Hits Ka Sorting & Naming](#5)** 📌  
6️⃣ **[Example: Elasticsearch Query with Inner Hits](#6)** 🧑‍💻  
7️⃣ **[Conclusion](#7)** 🎯  

---

## 1️⃣ 🏗️ Nested Queries Recap  <a id="1"></a>

- Humne **Nested Query** ka use kiya tha taaki **array of objects** ko alag-alag query kar sakein.  
- Jab humne **ingredients** ke andar **parmesan** ka amount >=100 filter kiya tha, tab hume unexpected results mile the.  
- Iska reason yeh tha ki Elasticsearch **objects ke relationships maintain nahi karta**, jab tak hum usse explicitly **nested data type** na banayein.  
- **Nested Query ka use karne ke baad**, humne correct filtering results paaye the.  

👀 **Lekin ek problem rahi:**  
Humne sirf **parent documents** dekhe, hume yeh nahi pata chala ki **kaunsa nested object match hua**.  

👉 **Solution?** **Inner Hits!** 🔥  

---

## 2️⃣ 🔎 Inner Hits Kya Hai?  <a id="2"></a>

**Inner Hits** ek additional feature hai jo batata hai ki **kaunsa nested object query se match hua hai**.  
- **Parent document milne ke saath-saath**, hum uske andar ke **matching nested objects** bhi dekh sakte hain.  
- Yeh useful hota hai jab **highlighting**, **debugging**, ya **detailed filtering** karni ho.  

📌 **Use Cases:**  
✅ **Debugging nested queries** – Agar query unexpected results de rahi hai, to hum jaan sakte hain kaunsa object match ho raha hai.  
✅ **Highlighting in UI** – Agar aap ek search engine bana rahe ho jo **user ke query se matching parts highlight** karta ho.  

---

## 3️⃣ ⚙️ Inner Hits Enable Kaise Karein?  <a id="3"></a>

Inner Hits ko **enable karna bahut simple hai**, bas hume **nested query ke andar** ek `inner_hits` parameter add karna hota hai.  

### ✅ **Basic Query with Inner Hits**  
```json
{
  "query": {
    "nested": {
      "path": "ingredients",
      "query": {
        "bool": {
          "must": [
            { "match": { "ingredients.name": "parmesan" } },
            { "range": { "ingredients.amount": { "gte": 100 } } }
          ]
        }
      },
      "inner_hits": {}
    }
  }
}
```
### 🏆 Expected Output:  
Ab hume sirf **parent recipe documents** nahi milenge, balki **uske andar match hone wale nested objects** bhi milenge! 🚀  

---

## 4️⃣ 🏛️ Inner Hits Ka Structure  <a id="4"></a>

Jab **inner_hits** enable hota hai, to Elasticsearch ke response me ek naya **inner_hits** object add ho jata hai:  

```json
{
  "hits": [
    {
      "_source": {
        "name": "Cheese Pasta",
        "ingredients": [
          { "name": "parmesan", "amount": 150, "unit": "grams" },
          { "name": "tomato", "amount": 200, "unit": "grams" }
        ]
      },
      "inner_hits": {
        "ingredients": {
          "hits": {
            "hits": [
              {
                "_nested": { "offset": 0 },
                "_source": { "name": "parmesan", "amount": 150, "unit": "grams" }
              }
            ]
          }
        }
      }
    }
  ]
}
```
💡 **Key Points:**  
- `inner_hits.ingredients.hits.hits` → Yeh **matching nested objects** ka array hai.  
- `_nested.offset` → Yeh batata hai ki **kaunsa object match hua** (index-wise).  
- `_source` → Yeh actual **nested object ka data** hai jo match hua.  

---

## 5️⃣ 📌 Inner Hits Ka Sorting & Naming  <a id="5"></a>

Hum **inner_hits** ko aur customize kar sakte hain:  
✅ **Sorting Inner Hits**: By default **relevance score** ke basis pe sort hota hai.  
✅ **Naming Inner Hits**: Agar multiple nested queries ek hi field pe ho, to hum `name` parameter se alag naam de sakte hain.  
✅ **Limiting Inner Hits**: Default 3 results aate hain, but hum ise `size` parameter se badha sakte hain.  

### ✅ **Customizing Inner Hits**  
```json
{
  "query": {
    "nested": {
      "path": "ingredients",
      "query": {
        "match": { "ingredients.name": "parmesan" }
      },
      "inner_hits": {
        "name": "matched_ingredients",
        "size": 5,
        "sort": [{ "ingredients.amount": "desc" }]
      }
    }
  }
}
```
💡 **Isse kya hoga?**  
- **Inner hits ka naam** `"matched_ingredients"` hoga.  
- **Max 5 results** aayenge (default 3 hota hai).  
- **Sorting ingredients.amount ke descending order** me hoga.  

---

## 6️⃣ 🧑‍💻 Example: Elasticsearch Query with Inner Hits  <a id="6"></a>

```json
{
  "query": {
    "nested": {
      "path": "ingredients",
      "query": {
        "bool": {
          "must": [
            { "match": { "ingredients.name": "parmesan" } },
            { "range": { "ingredients.amount": { "gte": 100 } } }
          ]
        }
      },
      "inner_hits": {
        "name": "matching_ingredients",
        "size": 10
      }
    }
  }
}
```
### 🎯 **Expected Output:**  
```
Recipe: "Cheese Pasta"
Matching Ingredient: Parmesan, Amount: 150 grams
```
✅ Sirf wohi **ingredients match** honge jo query ke criteria ko fulfill kar rahe hain!  

---

## 7️⃣ 🎯 Conclusion  <a id="7"></a>

- **Nested Queries** allow independent querying of objects inside arrays.  
- **Inner Hits** allow us to see which **specific nested objects matched** the query.  
- It helps in **debugging, filtering, and highlighting** results.  
- We can **sort, rename, and limit** inner hits for better control.  

