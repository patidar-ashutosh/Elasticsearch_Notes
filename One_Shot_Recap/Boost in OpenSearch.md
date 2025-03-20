# **📌 Boost in OpenSearch**  

## **📌 Introduction**  
**Boost ka use search results ki ranking improve karne ke liye hota hai.**  
- **Higher boost → Higher relevance** (Jo field ya document zyada important hai, usko search results me upar dikhane ke liye).  
- **Lower boost → Lower relevance** (Jo field ya document kam important hai, usko niche rakhne ke liye).  

📌 **Use Cases:**  
✔ **E-commerce Search** → Featured products ko higher rank dena.  
✔ **Job Portals** → Premium job listings ko zyada priority dena.  
✔ **Blog Search** → Latest ya featured articles ko upar dikhana.  

---

## **📌 Syntax (Short & Simple)**  
### **✅ Boost at Query Level**  
```json
GET index_name/_search
{
  "query": {
    "match": {
      "field_name": {
        "query": "search term",
        "boost": X
      }
    }
  }
}
```
🔹 **field_name** → Jis field ko boost dena hai.  
🔹 **query** → Search term jo match karna hai.  
🔹 **boost** → Higher value = Zyada priority (Jaise `2.0`, `3.0` etc.).  

---

## **✅ Example 1: Boost Title over Description**  
Agar ek **product catalog** hai, aur hume **title field ko zyada priority** deni hai **description ke comparison me**, to:  
```json
GET products/_search
{
  "query": {
    "bool": {
      "should": [
        { "match": { "title": { "query": "laptop", "boost": 3.0 } } },
        { "match": { "description": { "query": "laptop", "boost": 1.0 } } }
      ]
    }
  }
}
```
✔ **Title ko boost `3.0` diya gaya hai, iska matlab agar title me match hoga to result upar aayega.**  
✔ **Description me match hone par bhi aayega, but priority lower hogi (`1.0`).**  

---

## **✅ Example 2: Boost Specific Documents**  
Agar kisi **specific document ko higher rank dena hai**, to `_score` me manual boost add kar sakte hain:  
```json
GET products/_search
{
  "query": {
    "function_score": {
      "query": { "match": { "title": "laptop" } },
      "boost": 2.0,  
      "functions": [
        {
          "filter": { "term": { "_id": "123" } },
          "weight": 5
        }
      ],
      "score_mode": "multiply"
    }
  }
}
```
✔ **Agar document `_id: 123` hai, to uska score `5x` boost ho jayega.**  

---

## **📌 Key Takeaways**  
✔ **Boosting ka use relevance improve karne ke liye hota hai.**  
✔ **Query level boosting ek field ko zyada weightage deta hai.**  
✔ **Function score boosting se individual documents ko priority de sakte hain.**  
✔ **Boosting ko overuse mat karo, nahi to search results biased ho sakte hain.**  

