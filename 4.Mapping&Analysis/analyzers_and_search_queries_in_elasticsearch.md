# Analyzers & Search Queries in Elasticsearch

## Table of Contents
1. [Introduction](#introduction)
2. [Analyzers & Indexing](#analyzers--indexing)
3. [Analyzers & Search Queries](#analyzers--search-queries)
4. [How Elasticsearch Handles Queries](#how-elasticsearch-handles-queries)
5. [Case Insensitivity in Search](#case-insensitivity-in-search)
6. [Custom Analyzers](#custom-analyzers)
7. [Conclusion](#conclusion)

---

## Introduction
Analyzers Elasticsearch mein **text fields** ko process karne ke liye use hote hain. Jab hum documents ko index karte hain, tab analyzers text ko lowercase kar sakte hain, stemming apply kar sakte hain, aur stop words remove kar sakte hain. Yehi process search queries ke sath bhi hoti hai, taaki consistent aur relevant results mil sakein.

---

## Analyzers & Indexing
Jab hum ek **text field** ko index karte hain, Elasticsearch use analyze karta hai. Default **standard analyzer** ka behavior:

1. **Lowercasing:** Saare words ko lowercase mein convert karna.
2. **Tokenization:** Sentence ko individual words (tokens) mein todna.
3. **Removing punctuation:** Jaise comma, period, etc.
4. **Stemming (if applied):** Words ko unke root form mein convert karna (e.g., *drinking* → *drink*).

### Example:
Agar hum ek document ko store karte hain:

**Original Sentence:**
```
She is drinking water.
```

**Indexed Tokens (after stemming & analysis):**
```
she, be, drink, water
```

---

## Analyzers & Search Queries
Jab hum kisi document ko search karte hain, toh Elasticsearch queries ko bhi same analyzer se pass karta hai. Iska matlab jo process indexing ke waqt hui thi, wahi process **search query text** par bhi lagegi.

### Example:
Agar hum **"drinking"** search karte hain, toh Elasticsearch query ko bhi analyze karega aur "drinking" ko "drink" me convert karega. Kyunki **inverted index** me bhi "drink" store hai, toh query match ho jayegi.

**Query:**
```
GET my_index/_search
{
  "query": {
    "match": { "description": "drinking" }
  }
}
```

**Stored Index Tokens:**
```
drink, water
```

**Match Found! ✅**

Agar search query analyze nahi hoti, toh "drinking" aur "drink" alag tokens hote aur query match nahi hoti.

---

## How Elasticsearch Handles Queries
1. Query analyzer ko check karta hai jo field ke mapping mein defined hai.
2. Agar custom analyzer available hai, toh use apply karta hai, warna **standard analyzer** use hota hai.
3. Query ko analyze karke **processed terms** generate karta hai.
4. In terms ko inverted index ke against match karta hai.
5. Jo documents match hote hain, unhe retrieve karta hai aur relevance score ke according rank karta hai.

---

## Case Insensitivity in Search
Elasticsearch **case-insensitive** searching support karta hai, kyunki analyzers text ko **lowercase** mein convert kar dete hain.

### Example:
Agar koi query "WATER" likhta hai aur document mein "water" likha hai, toh query analyze hone ke baad dono "water" ho jayenge, aur match ho jayega.

---

## Custom Analyzers
Elasticsearch mein **custom analyzers** bhi bana sakte hain jo specific processing apply karte hain.

### Example:
Ek analyzer jo stemming aur lowercase apply karega:

```json
PUT my_index
{
  "settings": {
    "analysis": {
      "analyzer": {
        "custom_stemming_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": ["lowercase", "porter_stem"]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "description": {
        "type": "text",
        "analyzer": "custom_stemming_analyzer"
      }
    }
  }
}
```

Is analyzer mein:
- **Standard tokenizer** sentence ko words mein tod raha hai.
- **Lowercase filter** saare words ko chhoti letters mein kar raha hai.
- **Porter Stemmer filter** stemming apply kar raha hai.

---

## Conclusion
- **Analyzers indexing aur search queries dono ke liye use hote hain.**
- **Search queries ko bhi same analyze karna padta hai taaki expected results milein.**
- **Case insensitive searching Elasticsearch mein by default hoti hai.**
- **Custom analyzers create karke search performance aur results ko improve kiya ja sakta hai.**

