# 📌 Custom Analyzers in Elasticsearch

## 📜 Table of Contents  
1️⃣ [Custom Analyzer Kya Hota Hai?](#1)  
2️⃣ [Custom Analyzer Ka Structure](#2)  
3️⃣ [Character Filters](#3)  
4️⃣ [Tokenizers](#4)  
5️⃣ [Token Filters](#5)  
6️⃣ [Ek Complete Custom Analyzer Banana](#6)  
7️⃣ [Custom Analyzer Ko Test Karna](#7)  
8️⃣ [Language Specific Customization](#8)  
9️⃣ [Conclusion](#9)  

---

## 1️⃣ Custom Analyzer Kya Hota Hai? <a id="1"></a>
Elasticsearch me ek **analyzer** ka kaam hota hai text ko process karna taki uspar efficiently search kiya ja sake. Built-in analyzers hote hain jo already defined hote hain jaise **standard, simple, whitespace, stop**, etc.

Lekin kabhi kabhi hume custom requirements hoti hain, jaise **HTML tags remove karna, stop words hatana, lowercase karna, special characters normalize karna**, etc. Iske liye **Custom Analyzer** use hota hai jo **Character Filters, Tokenizer aur Token Filters** ka combination hota hai. 🔥

---

## 2️⃣ Custom Analyzer Ka Structure <a id="2"></a>
Custom analyzer ka structure kuch iss tarah hota hai:

```json
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_custom_analyzer": {
          "type": "custom",
          "char_filter": ["html_strip"],
          "tokenizer": "standard",
          "filter": ["lowercase", "stop", "asciifolding"]
        }
      }
    }
  }
}
```

📝 **Explanation:**
- `char_filter`: HTML tags remove karne ke liye.
- `tokenizer`: Words ko tokens me split karne ke liye.
- `filter`: Lowercase karna, stop words remove karna, aur special characters normalize karna.

---

## 3️⃣ Character Filters <a id="3"></a>
Character filters ka kaam **text me unwanted cheezein remove ya replace** karna hota hai. Example:

| Character Filter | Description |
|-----------------|-------------|
| `html_strip` | HTML tags remove karta hai |
| `mapping` | Specific characters ko replace karta hai |
| `pattern_replace` | Regex-based text replacement karta hai |

Example:
```json
"char_filter": ["html_strip"]
```

---

## 4️⃣ Tokenizers <a id="4"></a>
Tokenizer ka kaam text ko **tokens (words) me split** karna hota hai. Example:

| Tokenizer | Description |
|-----------|-------------|
| `standard` | Default tokenizer, punctuation ko split karta hai |
| `whitespace` | Sirf spaces par split karta hai |
| `keyword` | Pure text ko ek single token me rakhta hai |
| `pattern` | Regex-based split karta hai |

Example:
```json
"tokenizer": "standard"
```

---

## 5️⃣ Token Filters <a id="5"></a>
Token filters ka kaam **tokens par additional processing** karna hota hai. Example:

| Token Filter | Description |
|-------------|-------------|
| `lowercase` | Sab words ko lowercase me convert karta hai |
| `stop` | Common stop words remove karta hai |
| `asciifolding` | Special characters ko normalize karta hai |
| `stemmer` | Words ko root form me convert karta hai |

Example:
```json
"filter": ["lowercase", "stop", "asciifolding"]
```

---

## 6️⃣ Ek Complete Custom Analyzer Banana <a id="6"></a>
Chalo ek custom analyzer **banate hain aur use test bhi karte hain**! 🚀

### 🎯 Step 1: Index Create Karna
```json
PUT my_index
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_custom_analyzer": {
          "type": "custom",
          "char_filter": ["html_strip"],
          "tokenizer": "standard",
          "filter": ["lowercase", "stop", "asciifolding"]
        }
      }
    }
  }
}
```

---

## 7️⃣ Custom Analyzer Ko Test Karna <a id="7"></a>
Test karne ke liye **Analyze API** ka use karte hain:

```json
GET my_index/_analyze
{
  "analyzer": "my_custom_analyzer",
  "text": "<p>This is a Test! &copy; Café</p>"
}
```

**Output:**
```json
{
  "tokens": [
    {"token": "test"},
    {"token": "cafe"}
  ]
}
```
**Explanation:**
- `html_strip` ne `<p>` tag hata diya.
- `lowercase` ne "Test" ko "test" bana diya.
- `asciifolding` ne "Café" ko "cafe" bana diya.
- `stop` ne "This is a" jese words hata diye.

---

## 8️⃣ Language Specific Customization <a id="8"></a>
Agar **stop words** English ke alawa kisi aur language ke hone chahiye to hum **custom filter define** kar sakte hain:

```json
PUT my_index
{
  "settings": {
    "analysis": {
      "filter": {
        "danish_stop": {
          "type": "stop",
          "stopwords": "danish"
        }
      },
      "analyzer": {
        "danish_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": ["lowercase", "danish_stop"]
        }
      }
    }
  }
}
```

Agar **synonyms** handle karne hain to **synonym filter** bhi add kar sakte hain:
```json
"filter": {
  "synonym_filter": {
    "type": "synonym",
    "synonyms": ["laptop, notebook", "mobile, smartphone"]
  }
}
```

---

## 9️⃣ Conclusion <a id="9"></a>
Custom analyzers Elasticsearch me **text processing aur search ko improve** karne ke liye powerful tool hain. Tum **char filters, tokenizers, aur token filters** ko combine karke apne documents ke liye best search indexing bana sakte ho. 🔥

