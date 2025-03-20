# **📌 ngram in OpenSearch**  

## **📌 Introduction**  
**n-gram ek text analysis technique hai jo words ya phrases ko chhoti chhoti parts (substrings) me todta hai.**  
- Yeh **autocomplete search**, **partial matching**, aur **fuzzy search** ke liye use hota hai.  
- `n` define karta hai ki ek substring kitne characters ka hoga.  
  - **Bigram (2-gram)** → `"hello"` → `["he", "el", "ll", "lo"]`  
  - **Trigram (3-gram)** → `"hello"` → `["hel", "ell", "llo"]`  

📌 **Use Cases:**  
✔ **Autocomplete & Type-ahead Suggestions**  
✔ **Fuzzy Matching (Misspelled words ko bhi match karna)**  
✔ **Search Engine Improvement (Partial word matching)**  

---

## **📌 Syntax (Short & Simple)**  
### **✅ Step 1: Create Index with ngram Analyzer**
```json
PUT ngram_index
{
  "settings": {
    "analysis": {
      "tokenizer": {
        "ngram_tokenizer": {
          "type": "ngram",
          "min_gram": 2,
          "max_gram": 3,
          "token_chars": [ "letter", "digit" ]
        }
      },
      "analyzer": {
        "ngram_analyzer": {
          "type": "custom",
          "tokenizer": "ngram_tokenizer"
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "name": {
        "type": "text",
        "analyzer": "ngram_analyzer"
      }
    }
  }
}
```
✔ **Yeh 2-gram aur 3-gram generate karega.**  
✔ **"hello" ko `"he", "el", "ll", "lo", "hel", "ell", "llo"` me tod dega.**  

---

## **✅ Step 2: Insert Data**
```json
POST ngram_index/_doc/1
{
  "name": "OpenSearch"
}
```
✔ **"OpenSearch" ka n-gram breakdown hoga (`"Op", "pe", "en", "Ope", "pen", ...`)**  

---

## **✅ Step 3: Search Using Partial Match**
```json
GET ngram_index/_search
{
  "query": {
    "match": {
      "name": "Open"
    }
  }
}
```
✔ **Agar user sirf `"Op"` likhega tab bhi `"OpenSearch"` mil jayega.**  
✔ **Yeh normal match query se better hai kyunki yeh partial words bhi match karta hai.**  

---

## **📌 Key Takeaways**  
✔ **n-gram autocomplete aur fuzzy search ke liye best hai.**  
✔ **Minimum aur maximum gram size define karna padta hai (`min_gram`, `max_gram`).**  
✔ **Performance optimize karne ke liye `edge_ngram` bhi use kar sakte hain (yeh sirf starting characters ko tokenize karta hai).**  
✔ **Agar normal full-text search chahiye to `match` query better hai.**  

