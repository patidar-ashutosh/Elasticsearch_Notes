# 📊 Introduction to Relevance Scoring in Elasticsearch  

## 📌 Table of Contents  
1. 🔍 **Relevance Scoring Kya Hota Hai?**  
2. 📄 **_score Field Ka Role**  
3. 🎯 **Full-Text Queries vs. Term-Level Queries**  
4. 📑 **Example with Match Query**  
5. 🧮 **Relevance Score Calculation (Basic Overview)**  
6. 🔢 **Sorting Results by _score**  
7. 📌 **Key Takeaways**  

---

## 🔍 1. Relevance Scoring Kya Hota Hai?  
Jab bhi hum **Elasticsearch** me koi **search query** run karte hain, toh multiple documents match kar sakte hain. **Par kaunsa document sabse relevant hai?**  
Yahi kaam **Relevance Scoring** ka hota hai. Elasticsearch har matching document ko **_score** assign karta hai jo ye decide karta hai ki **kaunsa document sabse achha match karta hai** aur usko upar rank karega.  

Socho tum Google pe **"best pizza places"** search kar rahe ho. Tumhe expectation hoti hai ki sabse acche aur popular pizza places pehle aayein, na ki kisi random page pe. Elasticsearch me bhi **similar logic apply hota hai**.  

---

## 📄 2. _score Field Ka Role  
Jab hum **full-text search** karte hain, toh har document ke liye ek **_score field** generate hota hai jo relevance batata hai. **Jo document query ke zyada close hota hai, uska _score zyada hota hai.**  

**Example:**  
Agar hum **"pasta chicken"** search karte hain aur do documents hain:  
1. **"Delicious pasta with spicy chicken"**  
2. **"Pasta is great"**  

Toh **first document ka _score zyada hoga**, kyunki dono words ("pasta" aur "chicken") match ho rahe hain.  

---

## 🎯 3. Full-Text Queries vs. Term-Level Queries  
🔹 **Term-Level Queries:**  
- Yeh **exact match** ke liye hote hain.  
- Isme **_score ka koi role nahi hota**, kyunki bas check hota hai ki document match karta hai ya nahi (True/False).  
- Example: `term` query  

🔹 **Full-Text Queries:**  
- Yeh **approximate match** ke liye hote hain.  
- Isme relevance score calculate hota hai.  
- Example: `match` query  

👉 **_score sirf full-text queries me matter karta hai, term-level queries me nahi.**  

---

## 📑 4. Example with Match Query  
Chalo ek **match query** ka example dekhte hain:  

```json
GET my_index/_search
{
  "query": {
    "match": {
      "description": "pasta chicken"
    }
  }
}
```

### 🔍 Expected Output:
```json
{
  "hits": {
    "hits": [
      {
        "_id": "1",
        "_score": 1.8,
        "_source": { "description": "Delicious pasta with spicy chicken" }
      },
      {
        "_id": "2",
        "_score": 1.2,
        "_source": { "description": "Pasta is great" }
      }
    ]
  }
}
```

👉 **_score ke basis pe documents sort ho rahe hain**. Pehle **wo document aayega jisme dono words hain**, aur fir wo document jisme sirf ek word hai.  

---

## 🧮 5. Relevance Score Calculation (Basic Overview)  
Elasticsearch me **TF-IDF aur BM25 algorithms** use hote hain **_score calculate karne ke liye**. Basic factors jo affect karte hain:  

1. **Term Frequency (TF)** – Agar koi word ek document me zyada baar aaya hai, toh uska score zyada hoga.  
2. **Inverse Document Frequency (IDF)** – Rare words ka weight zyada hota hai.  
3. **Field Length Normalization** – Chhoti fields ka zyada weight hota hai.  

👉 **Relevance scoring ka pura process complex hai, lekin filhal itna samajhna zaroori hai ki zyada relevant documents ka _score zyada hota hai.**  

---

## 🔢 6. Sorting Results by _score  
Elasticsearch by default **search results ko _score ke descending order me sort karta hai**, yani **sabse relevant result sabse pehle aayega**.  

Agar manually sort karna ho, toh yeh query use kar sakte ho:  

```json
GET my_index/_search
{
  "query": {
    "match": {
      "description": "pasta chicken"
    }
  },
  "sort": [
    { "_score": "desc" }
  ]
}
```

👉 **By default bhi yahi sorting hoti hai, lekin agar kisi aur field pe sort karna ho (like date), toh explicitly define kar sakte ho.**  

---

## 📌 7. Key Takeaways  
✅ **Relevance Scoring ka kaam hota hai best results ko upar dikhana**.  
✅ **_score field sirf full-text queries ke liye matter karta hai, term-level queries ke liye nahi**.  
✅ **Documents jinme query ke zyada terms hote hain, unka _score zyada hota hai**.  
✅ **Elasticsearch results ko _score ke descending order me sort karta hai**.  
✅ **Relevance scoring algorithms like TF-IDF aur BM25 use hote hain**.  

---

