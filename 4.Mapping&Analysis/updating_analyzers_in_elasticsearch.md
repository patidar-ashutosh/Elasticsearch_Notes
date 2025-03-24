# Updating Analyzers in Elasticsearch

## 📜 **Table of Contents**
1️⃣ [Introduction](#1)  
2️⃣ [Adding an Analyzer to a Field Mapping](#2)  
3️⃣ [Indexing a Document](#3)  
4️⃣ [Searching with an Analyzer](#4)  
5️⃣ [Understanding the Issue with Stop Words](#5)  
6️⃣ [Updating the Analyzer](#6)  
7️⃣ [Verifying the Update](#7)  
8️⃣ [Handling Old Indexed Documents](#8)  
9️⃣ [Reindexing Documents](#9)  
🔟 [Conclusion](#10)  

---

## 1. Introduction <a id="1"></a>
Kabhi kabhi hume ek existing analyzer ko update karne ki zaroorat padti hai. Lekin, jaise mapping update karna mushkil hota hai, waise hi analyzers ko update karna bhi tricky ho sakta hai. Ideally, hume pehle hi sahi configuration set karni chahiye, par agar update karna zaroori ho, to uske liye kuch steps follow karne padenge.

Is document me hum dekhenge ki Elasticsearch me ek analyzer ko kaise update karte hain aur uske kya effects hote hain.

---

## 2. Adding an Analyzer to a Field Mapping <a id="2"></a>
Hum ek custom analyzer ko kisi field ke mapping me add kar sakte hain. For example, hum ek `description` field create karenge jo `my_custom_analyzer` ko use karega:

```json
PUT my_index
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_custom_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": ["lowercase", "stop"]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "description": {
        "type": "text",
        "analyzer": "my_custom_analyzer"
      }
    }
  }
}
```

---

## 3. Indexing a Document <a id="3"></a>
Ab hum ek document index karte hain:

```json
POST my_index/_doc/1
{
  "description": "This is a test document that contains some words."
}
```

---

## 4. Searching with an Analyzer <a id="4"></a>
Agar hum `description` field me "that" search karein, to koi result nahi milega, kyunki `stop` filter `that` ko remove kar raha hai.

```json
GET my_index/_search
{
  "query": {
    "match": {
      "description": "that"
    }
  }
}
```

Koi result nahi milega, kyunki analyzer ne "that" ko remove kar diya hai.

---

## 5. Understanding the Issue with Stop Words <a id="5"></a>
Yadi hum search query me analyzer specify karte hain, to hum result pa sakte hain:

```json
GET my_index/_search
{
  "query": {
    "match": {
      "description": {
        "query": "that",
        "analyzer": "keyword"
      }
    }
  }
}
```

Is trick ka use karke hum query me stop words ko preserve kar sakte hain.

---

## 6. Updating the Analyzer <a id="6"></a>
Ab hum `stop` filter hata kar analyzer ko update karenge. Lekin analyzer ek static setting hai, isliye hume pehle index close karna hoga:

```json
POST my_index/_close
```

Phir analyzer ko update karein:

```json
PUT my_index/_settings
{
  "analysis": {
    "analyzer": {
      "my_custom_analyzer": {
        "type": "custom",
        "tokenizer": "standard",
        "filter": ["lowercase"]
      }
    }
  }
}
```

Phir index ko wapas open karein:

```json
POST my_index/_open
```

---

## 7. Verifying the Update <a id="7"></a>
Index settings check karne ke liye:

```json
GET my_index/_settings
```

Isme hume analyzer ke naye settings dikhne chahiye.

---

## 8. Handling Old Indexed Documents <a id="8"></a>
Problem yeh hai ki pehle wale documents purane analyzer se index huye the. Agar ab hum search karein, to purane documents naye analyzer ke saath match nahi karenge.

```json
GET my_index/_search
{
  "query": {
    "match": {
      "description": "that"
    }
  }
}
```

Purane documents match nahi karenge, kyunki unko pehle wale analyzer se index kiya gaya tha.

---

## 9. Reindexing Documents <a id="9"></a>
Purane documents ko naye analyzer ke saath update karne ke liye `Update By Query API` ka use karein:

```json
POST my_index/_update_by_query
```

Yeh saare documents ko reindex kar dega, taki naye analyzer ka effect ho.

---

## 10. Conclusion <a id="10"></a>
- Pehle se indexed documents naye analyzer ke saath match nahi karenge.
- Isliye, ya to naye index me reindex karein, ya phir `Update By Query` ka use karein.
- Analyzer update karte waqt hamesha soch samajh kar decision lena chahiye, kyunki yeh search results pe effect daal sakta hai.

Agar production system me ho, to naye index banakar reindex karna best practice hai, taaki downtime na ho. 🚀

