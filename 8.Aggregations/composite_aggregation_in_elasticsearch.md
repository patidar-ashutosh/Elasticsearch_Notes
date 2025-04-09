# 📘 Composite Aggregation in Elasticsearch

## 🧠 Composite Aggregation kya hota hai?
Composite Aggregation Elasticsearch ka ek special type ka aggregation hai jo allow karta hai **multiple fields** pe group karne ke liye aur **pagination** ke saath buckets fetch karne ke liye.

Yeh use hota hai jab:

- Hame SQL-style `GROUP BY field1, field2` chahiye
- Ham custom order chahte ho script ke through
- Ham nested `terms` aggregation avoid karna chahte ho
- Ham large number of grouped results paginate karke laana chahte ho

---

## ✅ Composite Aggregation kyun use karein?
| Feature                          | Supported? |
|----------------------------------|------------|
| Multi-field grouping             | ✅          |
| Pagination (`after_key`)         | ✅          |
| Custom sort with scripts         | ✅          |
| Deterministic bucket ordering    | ✅          |
| Top hits per bucket              | ✅          |

---

## 🔧 Basic Syntax
```json
{
  "aggs": {
    "group_name": {
      "composite": {
        "size": 10,
        "sources": [
          { "field1": { "terms": { "field": "field1.keyword" } } },
          { "field2": { "terms": { "field": "field2.keyword" } } }
        ]
      }
    }
  }
}
```

---

## 🚀 Example Use Case: Group by `positionType` and `location`
```json
{
  "size": 0,
  "aggs": {
    "position_group": {
      "composite": {
        "size": 10,
        "sources": [
          { "positionType": { "terms": { "field": "positionType.keyword" } } },
          { "location": { "terms": { "field": "location.keyword" } } }
        ]
      }
    }
  }
}
```

---

## 🧠 Custom Order Using Script
```json
{
  "aggs": {
    "custom_ordered_group": {
      "composite": {
        "size": 100,
        "sources": [
          {
            "custom_order": {
              "terms": {
                "script": {
                  "source": """
                    if (doc['positionType.keyword'].value == 'Software-Development') return 0;
                    if (doc['positionType.keyword'].value == 'Network-Engineering') return 1;
                    if (doc['positionType.keyword'].value == 'IT-Support') return 2;
                    if (doc['positionType.keyword'].value == 'Data-Management') return 3;
                    return 4;
                  """,
                  "lang": "painless"
                }
              }
            }
          },
          {
            "positionType": {
              "terms": {
                "field": "positionType.keyword"
              }
            }
          }
        ]
      },
      "aggs": {
        "top_docs": {
          "top_hits": {
            "size": 5
          }
        }
      }
    }
  }
}
```

---

## 🔄 Pagination Using `after_key`
Jab buckets count `size` se zyada ho jaata hai, Elasticsearch hame `after_key` return karta hai. Jisko ham next request me use kar sakte hai:

```json
"after": {
  "positionType": "Network-Engineering",
  "location": "bangalore"
}
```

---

## 🔚 Summary
- `composite` aggregation se multi-field group-by possible hai
- Custom order chahiye to `script` use karo `terms` ke andar
- `top_hits` se har bucket me top N documents nikaal sakte hai
- `after_key` use karo pagination ke liye

---