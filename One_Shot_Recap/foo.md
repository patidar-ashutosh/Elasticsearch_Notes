# **📌 `foo.keyword` in OpenSearch**  

## **📌 Introduction**  
Jab hum OpenSearch me **text fields** pe sorting ya exact matching karna chahte hain, tab **`.keyword`** ka use hota hai.  
- OpenSearch me **text fields do parts me divided hote hain:**  
  1. **Analyzed (`text`)** → Yeh search ke liye tokenize aur process hota hai.  
  2. **Non-Analyzed (`keyword`)** → Yeh **exact match, sorting, aur aggregations** ke liye hota hai.  

📌 **Use Cases:**  
✔ **Sorting** (`name.keyword` ke basis pe A-Z ya Z-A order me).  
✔ **Exact matching** (Pura naam ya category match karna).  
✔ **Aggregations** (`terms` aggregation me categories count karna).  

---

## **📌 Syntax & Example**  
Agar hum ek field **"foo"** banate hain jo **text type** ka hai, to OpenSearch automatically **"foo.keyword"** create karta hai (agar mapping allow karti ho).  

### **✅ Mapping Example**
```json
PUT my_index
{
  "mappings": {
    "properties": {
      "foo": {
        "type": "text",
        "fields": {
          "keyword": { "type": "keyword" }
        }
      }
    }
  }
}
```
📌 **Yaha `"foo"` analyzed hai (search ke liye) aur `"foo.keyword"` exact match ke liye use hoga.**  

---

## **✅ Example 1: Sorting without `.keyword` (Error)**  
Agar hum **sorting** `"foo"` ke basis pe karne ki koshish karein, to error aayega:
```json
GET my_index/_search
{
  "sort": [
    { "foo": { "order": "asc" } }
  ]
}
```
### **❌ Error**
```json
{
  "type": "illegal_argument_exception",
  "reason": "Fielddata is disabled on text fields"
}
```
✔ **Sorting directly `text` field pe nahi ho sakti.**  

---

## **✅ Example 2: Sorting with `.keyword` (Correct)**
```json
GET my_index/_search
{
  "sort": [
    { "foo.keyword": { "order": "asc" } }
  ]
}
```
✔ **Ab sorting properly work karegi.**  

---

## **✅ Example 3: Exact Match Query**
Agar hume **exact match** chahiye to `.keyword` use karna padega:
```json
GET my_index/_search
{
  "query": {
    "term": {
      "foo.keyword": "OpenSearch"
    }
  }
}
```
✔ **Yeh sirf "OpenSearch" exact match karega, partial match nahi hoga.**  

---

## **✅ Example 4: Aggregation with `.keyword`**
Agar hume **category-wise count** chahiye, to `.keyword` use karna padega:
```json
GET my_index/_search
{
  "size": 0,
  "aggs": {
    "category_count": {
      "terms": {
        "field": "foo.keyword"
      }
    }
  }
}
```
✔ **Aggregation exact values ke basis pe kaam karega.**  

---

## **📌 Key Takeaways**  
✔ **`.keyword` non-analyzed hota hai (exact matching, sorting, aur aggregations ke liye).**  
✔ **Normal `text` fields sorting aur aggregations support nahi karte.**  
✔ **Exact match aur category-based analysis me `.keyword` useful hota hai.**  

