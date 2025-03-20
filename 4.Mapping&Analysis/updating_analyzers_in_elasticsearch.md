# Updating Analyzers in Elasticsearch

## Table of Contents
1. [Introduction](#introduction)
2. [Adding an Analyzer to a Field Mapping](#adding-an-analyzer-to-a-field-mapping)
3. [Indexing a Document](#indexing-a-document)
4. [Searching with an Analyzer](#searching-with-an-analyzer)
5. [Understanding the Issue with Stop Words](#understanding-the-issue-with-stop-words)
6. [Updating the Analyzer](#updating-the-analyzer)
7. [Verifying the Update](#verifying-the-update)
8. [Handling Old Indexed Documents](#handling-old-indexed-documents)
9. [Reindexing Documents](#reindexing-documents)
10. [Conclusion](#conclusion)

---

## Introduction
Kabhi kabhi hume ek existing analyzer ko update karne ki zaroorat padti hai. Lekin, jaise mapping update karna mushkil hota hai, waise hi analyzers ko update karna bhi tricky ho sakta hai. Ideally, hume pehle hi sahi configuration set karni chahiye, par agar update karna zaroori ho, to uske liye kuch steps follow karne padenge.

Is document me hum dekhenge ki Elasticsearch me ek analyzer ko kaise update karte hain aur uske kya effects hote hain.

---

## Adding an Analyzer to a Field Mapping
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

## Indexing a Document
Ab hum ek document index karte hain:

```json
POST my_index/_doc/1
{
  "description": "This is a test document that contains some words."
}
```

---

## Searching with an Analyzer
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

## Understanding the Issue with Stop Words
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

## Updating the Analyzer
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

## Verifying the Update
Index settings check karne ke liye:

```json
GET my_index/_settings
```

Isme hume analyzer ke naye settings dikhne chahiye.

---

## Handling Old Indexed Documents
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

## Reindexing Documents
Purane documents ko naye analyzer ke saath update karne ke liye `Update By Query API` ka use karein:

```json
POST my_index/_update_by_query
```

Yeh saare documents ko reindex kar dega, taki naye analyzer ka effect ho.

---

## Conclusion
- Pehle se indexed documents naye analyzer ke saath match nahi karenge.
- Isliye, ya to naye index me reindex karein, ya phir `Update By Query` ka use karein.
- Analyzer update karte waqt hamesha soch samajh kar decision lena chahiye, kyunki yeh search results pe effect daal sakta hai.

Agar production system me ho, to naye index banakar reindex karna best practice hai, taaki downtime na ho. 🚀

