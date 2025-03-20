# 📖 Introduction to Full Text Queries

## 📌 Table of Contents
1. 🔍 **Full Text Queries Kya Hain?**  
2. 🎯 **Term Level Queries vs Full Text Queries**  
3. 📝 **Full Text Queries Kaam Kaise Karti Hain?**  
4. 🔄 **Query Analysis Ka Role**  
5. ⚡ **Full Text Queries vs Exact Matching**  
6. 🛠 **Example with Output**  

---

## 🔍 Full Text Queries Kya Hain?

Ab tak hum **term level queries** ke baare me padh chuke hain. Ab baat karte hain **full text queries** ki. Yeh queries **unstructured text data** (jaise ki blog posts, news articles, comments, etc.) me searching ke liye use hoti hain.

Jab hum Google ya kisi mail application me kuch search karte hain, toh hum exact match nahi dhoondh rahe hote, balki **specific words ya phrases** ko search kar rahe hote hain. **Full text search ka kaam bhi wahi hai – unstructured text me se relevant documents dhoondhna.**

---

## 🎯 Term Level Queries vs Full Text Queries

| Feature | Term Level Queries | Full Text Queries |
|---------|------------------|------------------|
| **Data Type** | Structured data | Unstructured text |
| **Matching** | Exact match | Partial match / relevant results |
| **Use Case** | IDs, keywords, tags | Blog posts, emails, articles |
| **Query Type** | Not analyzed | Analyzed |

Agar ek field **text type** ki hai, toh uske liye **full text queries** use karni chahiye. Agar **keyword type** ki field hai, toh **term level queries** ka use karna chahiye.

---

## 📝 Full Text Queries Kaam Kaise Karti Hain?

- Full text queries **analyzed hoti hain**, iska matlab hai ki Elasticsearch query ko breakdown karta hai aur fir search karta hai.
- Jab ek query run hoti hai, toh Elasticsearch mapping check karta hai aur agar **analyzer configured** hai toh use apply karta hai.
- Analyzer **query ko process karta hai** waise hi jaise indexing ke time document ke text ko process kiya jata hai.
- **Agar query analyzed na ho, toh uppercase aur lowercase words ko alag treat kiya jayega, jo search results ko affect karega.**

Example: Agar hum **"SHARDING"** search karein aur analyzer query ko lowercase kar de, toh **"sharding"** ko match karega, warna nahi.

---

## 🔄 Query Analysis Ka Role

Jab documents index hote hain, toh unka text **analyzed** hota hai:
1. **Lowercasing:** "SHARDING" -> "sharding"
2. **Stopwords Removal:** "This is a test" -> "test"
3. **Stemming:** "Running" -> "Run"

Jab hum koi full text query chalate hain, toh **query bhi analyze hoti hai**, taaki **search aur indexed data ka format same ho.**

---

## ⚡ Full Text Queries vs Exact Matching

- **Term level queries** **exact match** ke liye use hoti hain.
- **Full text queries** **partial match ya relevant results** ke liye use hoti hain.
- **Full text queries ko keyword fields ke saath use nahi karna chahiye, kyunki keywords analyze nahi hote.**

---

## 🛠 Example with Output

### ✅ Data Indexing

```json
PUT posts/_doc/1
{
  "title": "Elasticsearch Basics",
  "body": "Sharding helps distribute data across nodes."
}
```

```json
PUT posts/_doc/2
{
  "title": "Full Text Search",
  "body": "Full text queries analyze the search term."
}
```

### 🔍 Full Text Query Example

```json
GET posts/_search
{
  "query": {
    "match": {
      "body": "sharding"
    }
  }
}
```

### 📌 Output

```json
{
  "hits": {
    "total": 1,
    "hits": [
      {
        "_source": {
          "title": "Elasticsearch Basics",
          "body": "Sharding helps distribute data across nodes."
        }
      }
    ]
  }
}
```

📌 **Yeh query sirf wahi document match karegi jisme "sharding" word present hai.**

---

## 🎯 Conclusion

- **Full text queries** ka use **unstructured text** ko search karne ke liye hota hai.
- **Query analyze hoti hai**, taaki search aur indexed text same format me ho.
- **Term level queries sirf exact matches ke liye use hoti hain.**
- **Full text queries keyword fields ke liye suitable nahi hoti.**

