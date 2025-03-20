# 📑 Table of Contents

1. 🔍 **Introduction to Searching Multiple Fields**  
2. 🎯 **What is multi_match Query?**  
3. ⚙️ **Basic Syntax & Example**  
4. 📊 **Relevance Scoring in multi_match**  
5. 📈 **Boosting Specific Fields**  
6. 🔗 **Types of multi_match Queries**  
7. 🏆 **Using tie_breaker for Better Relevance**  
8. 🚀 **Advanced Use Cases & Optimization**  
9. 📌 **Conclusion**  

---

## 🔍 1. Introduction to Searching Multiple Fields

Jab hum **Elasticsearch** me search karte hain, to aksar ek hi field ka match dekhna hota hai. Par kabhi kabhi humein **multiple fields** me ek hi query ko apply karna padta hai. Jaise:

- **Product Search** me **name aur description** dono check karna
- **Blogs Search** me **title aur content** dono match hona
- **E-commerce** me **category aur tags** dono ka relevance dekhna

Iske liye **multi_match** query ka use hota hai jo ek powerful tool hai.

---

## 🎯 2. What is multi_match Query?

**multi_match query** ek advanced query hai jo ek **single search term** ko **multiple fields** me search karne ki ability deti hai. Yeh **match query** ka ek extension hai, jisme hum **multiple fields** ko specify kar sakte hain.

Agar kisi **ek field me match** milta hai, to document search results me aa jata hai.

---

## ⚙️ 3. Basic Syntax & Example

Agar hum **name** aur **tags** dono me "vegetable" ko search karna chahein, to query kuch aise likhi jayegi:

```json
{
  "query": {
    "multi_match": {
      "query": "vegetable",
      "fields": ["name", "tags"]
    }
  }
}
```

### 📝 Expected Output:

```json
{
  "hits": {
    "total": 5,
    "hits": [
      { "_source": { "name": "Green Vegetable", "tags": ["fresh", "organic"] } },
      { "_source": { "name": "Vegetable Soup", "tags": ["healthy", "delicious"] } }
    ]
  }
}
```

📌 **Note:** Agar "vegetable" sirf **tags** me ho ya sirf **name** me ho, tab bhi document match karega.

---

## 📊 4. Relevance Scoring in multi_match

Har **matching document** ko Elasticsearch ek **_score** assign karta hai jo relevance define karta hai. **multi_match query** by default **sabse high score wale field** ka score use karti hai.

Example:

| Document | Name Score | Tags Score | Final Score |
|----------|-----------|------------|-------------|
| Doc 1    | 5.5       | 3.0        | 5.5         |
| Doc 2    | 2.0       | 6.8        | 6.8         |

Final score **sabse zyada score wale field** ka hota hai.

---

## 📈 5. Boosting Specific Fields

Agar kisi **specific field ko zyada importance** deni ho to uska **weightage** badhaya ja sakta hai **caret symbol (^)** ke sath.

```json
{
  "query": {
    "multi_match": {
      "query": "vegetable",
      "fields": ["name^2", "tags"]
    }
  }
}
}
```

✅ **Effect:**
- **name** field ka relevance **2x** ho jayega
- Agar **vegetable** name field me milega to wo **higher rank** karega

---

## 🔗 6. Types of multi_match Queries

multi_match query me **4 important types** hote hain:

| Type           | Description |
|---------------|------------|
| best_fields   | Default mode, highest matching field ka score leta hai |
| most_fields   | Multiple fields ke scores ko combine karta hai |
| cross_fields  | Search term ko multiple fields me split karke dekhta hai |
| phrase        | Words ki exact order ko match karta hai |

Example:
```json
{
  "query": {
    "multi_match": {
      "query": "organic vegetable",
      "fields": ["name", "tags"],
      "type": "phrase"
    }
  }
}
```

✅ **Effect:** Yeh query sirf **exact phrase "organic vegetable"** ko match karegi.

---

## 🏆 7. Using tie_breaker for Better Relevance

By default, sirf **best matching field ka score** liya jata hai. **tie_breaker** ka use karke **baaki matching fields ka effect bhi include** kar sakte hain.

```json
{
  "query": {
    "multi_match": {
      "query": "vegetable broth",
      "fields": ["name", "description"],
      "tie_breaker": 0.3
    }
  }
}
```

✅ **Effect:** Agar **name** ka score 12.69 hai aur **description** ka 8.51 hai,

```
Final Score = 12.69 + (8.51 * 0.3) = 15.24
```

📌 **Benefit:** Yeh **documents ko reward** karta hai jisme multiple fields match ho rahe hain.

---

## 🚀 8. Advanced Use Cases & Optimization

- **Synonyms Handling**: Agar ek hi cheez ke multiple names hain (e.g., "TV" = "Television"), to **synonyms analyzer** use karna better hoga.
- **Nested Fields**: Agar documents complex ho (e.g., reviews ke andar user comments ho), to **nested query** ka use better hoga.
- **Performance Optimization**: Bahut zyada fields search karna expensive ho sakta hai, to sirf **important fields** define karna best practice hai.

---

## 📌 9. Conclusion

- **multi_match query** ek **powerful tool** hai jo **multiple fields me search** karne ki flexibility deta hai.
- **Boosting aur tie_breaker** ka use karke relevance ko **fine-tune** kiya ja sakta hai.
- Different **types** of multi_match query **different use cases** ke liye best suited hote hain.

