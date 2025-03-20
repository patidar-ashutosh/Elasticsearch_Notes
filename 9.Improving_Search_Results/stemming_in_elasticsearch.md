# 📌 Stemming in Elasticsearch

## 📖 Table of Contents
1. 🔍 **Introduction to Stemming**
2. 🛠 **Why Use Stemming?**
3. ⚙ **How Stemming Works in Elasticsearch**
4. 🚀 **Example: Applying Stemming**
5. 🎭 **Combining Stemming with Synonyms**
6. 🔦 **Highlighting Stemmed Words**
7. 🏁 **Conclusion**

---

## 🔍 1. Introduction to Stemming

**Stemming** ek process hai jisme words ko unke root form me convert kiya jata hai taki search results zyada flexible ho sakein.

🔹 Example:
- "working" ➝ "work"
- "loved" ➝ "love"
- "running" ➝ "run"

Elasticsearch stemming ka use karta hai better search matches ke liye, taaki similar words ko ek hi category me treat kiya ja sake.

---

## 🛠 2. Why Use Stemming?

✅ **Improved Search Matches** – Users jo "running" search karein, unhe "run" waale results bhi mil sakein.  
✅ **Better User Experience** – Synonyms aur similar words automatically match ho sakein.  
✅ **Efficient Query Processing** – Similar words ek hi stemmed form me store ho jayein, reducing complexity.

---

## ⚙ 3. How Stemming Works in Elasticsearch

**Stemming Elasticsearch ke Token Filters ke through implement hota hai.**

👉 Elasticsearch me **`stemmer` filter** use hota hai jo words ko root form me convert karta hai.

**Example: Custom Analyzer with Stemming**

```json
PUT stemming_test
{
  "settings": {
    "analysis": {
      "analyzer": {
        "custom_stem_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": ["lowercase", "stemmer_test"]
        }
      },
      "filter": {
        "stemmer_test": {
          "type": "stemmer",
          "name": "english"
        }
      }
    }
  }
}
```

👆 **Yeh analyzer words ko lowercase me convert karega aur phir stemming apply karega.**

---

## 🚀 4. Example: Applying Stemming

### **Step 1: Index a Document**

```json
PUT stemming_test/_doc/1
{
  "description": "I love working for my firm!"
}
```

💡 **Yahan "working" ko Elasticsearch automatically "work" me stem karega.**

### **Step 2: Search with Stemming**

```json
POST stemming_test/_search
{
  "query": {
    "match": {
      "description": "work"
    }
  }
}
```

### 📜 **Expected Output:**

```json
{
  "hits": {
    "hits": [
      {
        "_source": {
          "description": "I love working for my firm!"
        }
      }
    ]
  }
}
```

✅ "working" match ho gaya kyunki "work" stemmed form hai!

---

## 🎭 5. Combining Stemming with Synonyms

Agar tum stemming ko **synonyms ke saath combine** karna chaho, to Elasticsearch automatically synonyms ko bhi stemmed form me analyze karega.

**Example:**

```json
PUT stemming_test/_doc/2
{
  "description": "I love working for my firm!"
}
```

🔹 **Synonyms Mapping:**

```json
{
  "filter": {
    "synonym_test": {
      "type": "synonym",
      "synonyms": [
        "firm, company",
        "love, enjoy"
      ]
    }
  }
}
```

### **Search with Synonyms & Stemming:**

```json
POST stemming_test/_search
{
  "query": {
    "match": {
      "description": "enjoy work"
    }
  }
}
```

✅ **Even though "enjoy" and "work" words document me nahi hai, match ho jayega kyunki "love" synonym hai aur "working" stemmed form hai!**

---

## 🔦 6. Highlighting Stemmed Words

Agar tum chaho ki stemmed words search results me **highlight** ho jayein, to tum Elasticsearch highlighter use kar sakte ho.

### **Example: Highlighting in Search Query**

```json
POST stemming_test/_search
{
  "query": {
    "match": {
      "description": "enjoy work"
    }
  },
  "highlight": {
    "fields": {
      "description": {}
    }
  }
}
```

### 📜 **Expected Output:**

```json
{
  "hits": {
    "hits": [
      {
        "_source": {
          "description": "I love working for my firm!"
        },
        "highlight": {
          "description": [
            "I <em>love</em> <em>working</em> for my firm!"
          ]
        }
      }
    ]
  }
}
```

✅ **"Love" and "Working" highlight ho gaye even though humne "Enjoy" aur "Work" search kiya tha!**

---

## 🏁 7. Conclusion

✅ **Stemming helps in matching words in their root form.**  
✅ **It improves search accuracy and flexibility.**  
✅ **When combined with synonyms, it creates a powerful search engine.**  
✅ **Elasticsearch still highlights original words even after stemming.**  

🔹 **Next Step:** Advanced Stemming techniques like `porter_stem`, `kstem`, aur `lemmatization` explore kar sakte ho! 🚀

---

