# Analyzers & Search Queries in Elasticsearch

## **📌 Table of Contents**  
1️⃣ [Introduction](#1)  
2️⃣ [Analyzers & Indexing](#2)  
3️⃣ [Analyzers & Search Queries](#3)  
4️⃣ [How Elasticsearch Handles Queries](#4)  
5️⃣ [Case Insensitivity in Search](#5)  
6️⃣ [Custom Analyzers](#6)  
7️⃣ [Conclusion](#7)  

---

## 1. Introduction <a id="1"></a>
Analyzers Elasticsearch mein **text fields** ko process karne ke liye use hote hain. Jab hum documents ko index karte hain, tab analyzers text ko lowercase kar sakte hain, stemming apply kar sakte hain, aur stop words remove kar sakte hain. Yehi process search queries ke sath bhi hoti hai, taaki consistent aur relevant results mil sakein.

---

## 2. Analyzers & Indexing <a id="2"></a>
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

## 3. Analyzers & Search Queries <a id="3"></a>
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

## 4. How Elasticsearch Handles Queries <a id="4"></a>
1. Query analyzer ko check karta hai jo field ke mapping mein defined hai.
2. Agar custom analyzer available hai, toh use apply karta hai, warna **standard analyzer** use hota hai.
3. Query ko analyze karke **processed terms** generate karta hai.
4. In terms ko inverted index ke against match karta hai.
5. Jo documents match hote hain, unhe retrieve karta hai aur relevance score ke according rank karta hai.

---

## 5. Case Insensitivity in Search <a id="5"></a>
Elasticsearch **case-insensitive** searching support karta hai, kyunki analyzers text ko **lowercase** mein convert kar dete hain.

### Example:
Agar koi query "WATER" likhta hai aur document mein "water" likha hai, toh query analyze hone ke baad dono "water" ho jayenge, aur match ho jayega.

---

## 6. Custom Analyzers <a id="6"></a>
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

## 7. Conclusion <a id="7"></a>
- **Analyzers indexing aur search queries dono ke liye use hote hain.**
- **Search queries ko bhi same analyze karna padta hai taaki expected results milein.**
- **Case insensitive searching Elasticsearch mein by default hoti hai.**
- **Custom analyzers create karke search performance aur results ko improve kiya ja sakta hai.**

