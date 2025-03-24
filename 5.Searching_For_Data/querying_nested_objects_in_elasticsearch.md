# 📌 Querying Nested Objects in Elasticsearch  

## 📜 Table of Contents  

1. 🏗 [**Nested Objects vs. Regular Objects**](#1)  
2. 🔍 [**Problem with Querying Nested Objects**](#2)  
3. 🛠 [**Fixing the Issue with Nested Data Type**](#3)  
4. 📊 [**How Elasticsearch Indexes Nested Documents**](#4)  
5. 📌 [**Querying Nested Fields Properly**](#5)  
6. 🏆 [**Relevance Scoring in Nested Queries**](#6)  
7. 📝 [**Final Thoughts & Key Takeaways**](#7)  

---  

## 1. 🏗 **Nested Objects vs. Regular Objects**  <a id="1"></a>

Elasticsearch me hum **object fields** ko store kar sakte hain, jo ek **array of objects** bhi ho sakte hain. **Lekin problem tab aati hai jab hume kisi specific object ke andar specific field ko query karna hota hai.**  

### **Example:**  
Imagine karo ek **recipe index** hai jisme ingredients ek **array of objects** hain:  

```json
{
  "name": "Pasta",
  "ingredients": [
    { "name": "parmesan", "amount": 100, "unit": "grams" },
    { "name": "cheese", "amount": 200, "unit": "grams" }
  ]
}
```

**Ab problem yeh hai ki agar hum ek bool query likhein jo parmesan ko **100 grams ya usse zyada** amount me dhoondhne ki koshish kare, to query fail ho sakti hai!**  

---  

## 2. 🔍 **Problem with Querying Nested Objects**  <a id="2"></a>

Elasticsearch me **arrays of objects** ko **flattened structure** me store kiya jata hai, **jisme relationships lose ho jati hain.**  

**Example:** Agar hum ek query likhein jo **"parmesan" aur "amount >= 100"** ko match kare, to problem ho sakti hai:  

```json
{
  "query": {
    "bool": {
      "must": [
        { "match": { "ingredients.name": "parmesan" } },
        { "range": { "ingredients.amount": { "gte": 100 } } }
      ]
    }
  }
}
```

#### ❌ **Wrong Results Milenge!**  
Kyun? Kyunki **Elasticsearch sare values ko alag alag check karta hai, bina kisi object relationship ko maintain kiye.**  

For example, agar ek document me **parmesan** hai, aur kisi **dusre ingredient ka amount 100 se zyada hai**, to bhi wo document match ho jayega, jo galat hai!  

---  

## 3. 🛠 **Fixing the Issue with Nested Data Type**  <a id="3"></a>

Solution hai **nested data type** use karna!  

Agar hume **ingredients ke andar relationships maintain** karni hain, to hume **nested mapping** define karni padegi:  

```json
{
  "mappings": {
    "properties": {
      "ingredients": {
        "type": "nested",
        "properties": {
          "name": { "type": "text" },
          "amount": { "type": "integer" },
          "unit": { "type": "keyword" }
        }
      }
    }
  }
}
```

Iske baad **hum accurate queries likh sakte hain jo objects ke andar sahi relationships ko preserve karein.**  

---  

## 4. 📊 **How Elasticsearch Indexes Nested Documents**  <a id="4"></a>

Jab hum **nested mapping** use karte hain, to Elasticsearch **hidden Lucene documents** create karta hai.  

Agar ek recipe me **10 ingredients** hain, to Elasticsearch **11 documents** create karega:  
1. **1 parent document (recipe)**  
2. **10 nested documents (har ek ingredient ke liye ek document)**  

**Yeh indexing ka structure ensure karta hai ki har nested object ka data independent rahe.**  

---  

## 5. 📌 **Querying Nested Fields Properly**  <a id="5"></a>

Ab jab humne **nested mapping** define kar di hai, to hum **nested queries** likh sakte hain jo sahi results return karein.  

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
      }
    }
  }
}
```

### ✅ **Expected Output:**  
Sirf wahi recipes return hongi jisme **parmesan ka amount >= 100 hai.**  

---  

## 6. 🏆 **Relevance Scoring in Nested Queries**  <a id="6"></a>

Nested queries ke saath **relevance score** ko control karne ke liye **score_mode** parameter use hota hai.  

1. **"avg"** (Default) - Sabhi matching child objects ka **average score** calculate karega  
2. **"min"** - Sabse **kam score** select karega  
3. **"max"** - Sabse **zyada score** select karega  
4. **"sum"** - Sabhi child objects ka **total sum** karega  
5. **"none"** - Child objects ka **score ignore** karega (0 use karega)  

```json
{
  "query": {
    "nested": {
      "path": "ingredients",
      "query": {
        "match": { "ingredients.name": "parmesan" }
      },
      "score_mode": "max"
    }
  }
}
```

---  

## 7. 📝 **Final Thoughts & Key Takeaways**  <a id="7"></a>

✅ **Nested data type use karo jab bhi tumhe object relationships maintain karni ho.**  
✅ **Normal object fields flatten ho jate hain, jo incorrect query results de sakte hain.**  
✅ **Nested objects ko query karne ke liye nested query ka use karo.**  
✅ **Relevance scoring ko customize karne ke liye score_mode ka use karo.**  

