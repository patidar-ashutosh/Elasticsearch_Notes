# Reindexing Documents with the Reindex API

## **📌 Table of Contents**  
1️⃣ [Introduction](#1)  
2️⃣ [Reindexing Ka Kya Matlab Hai?](#2)  
3️⃣ [Naya Index Create Karna](#3)  
4️⃣ [Reindex API Ka Basic Usage](#4)  
5️⃣ [Reindexing Ke Dauraan Document Modify Karna](#5)  
6️⃣ [Kuch Specific Documents Hi Reindex Karna](#6)  
7️⃣ [Field Ko Remove Ya Rename Karna](#7)  
8️⃣ [Advanced Reindexing Features](#8)  
9️⃣ [Performance Optimization](#9)  
🔟 [Conclusion](#10)  

---

## 1. Introduction <a id="1"></a>
Reindex API ka use hum tab karte hain jab humein **documents ko ek index se dusre index me move** karna hota hai, bina manually retrieve aur reinsert kiye. Yeh process bahut important hota hai jab:
- Mapping change karna ho
- Index settings update karni ho
- Data ko optimize karna ho

## 2. Reindexing Ka Kya Matlab Hai? <a id="2"></a>
Reindexing ka matlab hai documents ko ek purane index se nikal kar ek naye index me insert karna. Yeh tab zaroori hota hai jab humein kisi field ka data type change karna ho, jaise ki **"product_id" ko integer se keyword** me convert karna.

🚀 **Iska Use Kab Hota Hai?**  
1. **Index structure change karne ke liye** (e.g., number of shards badalne ke liye)  
2. **Mapping ya settings update karne ke liye**  
3. **Old index ka data new index me migrate karne ke liye**  
4. **Data filtering aur transformation karne ke liye**  


## 3. Naya Index Create Karna <a id="3"></a>
Reindexing start karne se pehle humein **naya index create karna hota hai** jisme updated mappings aur settings hoti hain. Example:

```json
PUT /reviews_v2
{
  "mappings": {
    "properties": {
      "product_id": { "type": "keyword" }
    }
  }
}
```

## 4. Reindex API Ka Basic Usage <a id="4"></a>
Reindex API ek simple **POST request** hoti hai jo **_reindex** endpoint pe send ki jati hai:

```json
POST _reindex
{
  "source": {
    "index": "reviews"
  },
  "dest": {
    "index": "reviews_v2"
  }
}
```

Yeh query **"reviews" index ke sabhi documents ko "reviews_v2" index me copy** kar degi.

## 5. Reindexing Ke Dauraan Document Modify Karna <a id="5"></a>
Agar humein documents me kuch changes karne hain, toh hum **script** ka use kar sakte hain. Example:

```json
POST _reindex
{
  "source": {
    "index": "reviews"
  },
  "dest": {
    "index": "reviews_v2"
  },
  "script": {
    "source": "ctx._source.product_id = ctx._source.product_id.toString()"
  }
}
```

Yeh script **product_id field ko string me convert** kar degi.

## 6. Kuch Specific Documents Hi Reindex Karna <a id="6"></a>
Agar humein sirf kuch specific documents ko reindex karna hai, toh hum **query filter** use kar sakte hain. Example:

```json
POST _reindex
{
  "source": {
    "index": "reviews",
    "query": {
      "range": {
        "rating": {
          "gte": 4.0
        }
      }
    }
  },
  "dest": { "index": "reviews_v2" }
}
```

Yeh query sirf **rating 4.0 ya usse zyada wale documents** ko reindex karegi.

## 7. Field Ko Remove Ya Rename Karna <a id="7"></a>
### Field Remove Karna
Agar kisi field ko reindexing ke dauraan **remove** karna ho, toh hum **_source filtering** use kar sakte hain:

```json
POST _reindex
{
  "source": {
    "index": "reviews"
  },
  "dest": {
    "index": "reviews_v2"
  },
  "_source": ["product_id", "rating"]
}
```

Yeh query sirf **product_id aur rating** ko reindex karegi, baaki fields remove ho jayengi.

### Field Rename Karna
Agar kisi field ka **naam change** karna ho, toh script ka use kar sakte hain:

```json
POST _reindex
{
  "source": {
    "index": "reviews"
  },
  "dest": {
    "index": "reviews_v2"
  },
  "script": {
    "source": "ctx._source.comment = ctx._source.remove('content')"
  }
}
```

Yeh query **"content" field ka naam "comment"** kar degi.

## 8. Advanced Reindexing Features <a id="8"></a>
1. **Version Conflicts**: Agar documents pehle se exist kar rahe hain toh **version conflicts** aa sakti hain. Isse handle karne ke liye:
  ```json
  POST _reindex
  {
    "source": {
    "index": "reviews"
    },
    "dest": {
    "index": "reviews_v2"
    },
    "conflicts": "proceed"
  }
  ```

2. **Batch Processing**: Badi indexing me **Scroll API** use hoti hai jo performance improve karti hai.

## 9. Performance Optimization <a id="9"></a>
- **Throttling**: Agar load kam karna ho toh **requests per second** limit set kar sakte hain:
```json
POST _reindex
{
  "source": {
    "index": "reviews"
  },
  "dest": {
    "index": "reviews_v2"
  },
  "max_docs": 1000,
  "scroll": "5m"
}
```
- **Delete By Query**: Agar reindex ke baad purane documents delete karne hain toh:
```json
POST reviews/_delete_by_query
{
  "query": {
    "match_all": {}
  }
}
```

## 10. Conclusion <a id="10"></a>
Reindex API **documents ko migrate aur modify karne ka sabse efficient way** hai. Isse hum **mapping changes, field modifications, aur performance optimization** kar sakte hain. Agar bahut bade datasets handle kar rahe ho toh **throttling aur batch processing** ka dhyan rakhna zaroori hai.


---
[Reference Video Link](https://youtu.be/7gj629THDCk?si=NBq-3qZFtb0dqHg5)
---