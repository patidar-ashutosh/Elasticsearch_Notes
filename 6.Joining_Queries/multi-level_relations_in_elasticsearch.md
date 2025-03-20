**multi-level relations** ka concept **parent-child relationships** ka ek advanced form hai. Isme ek **hierarchical structure** create hota hai, jisme ek entity ke multiple child entities ho sakti hain, aur unke bhi child entities ho sakti hain.  

### 🔥 **Multi-Level Relations in Elasticsearch**  
Agar **single-level relation** me ek **parent-child** ka connection hota hai, to **multi-level relation** me **nested hierarchical** relation hota hai, jaise:  
- **Company** -> **Department, Supplier**  
- **Department** -> **Employees**  

Ye useful hota hai **complex organizational data structures** ko store aur query karne ke liye.  

---  

## 📜 **Table of Contents**  

1️⃣ **🔍 Multi-Level Relations Kya Hote Hain?**  
2️⃣ **🏗️ Index aur Mapping Setup**  
3️⃣ **📄 Documents ko Insert Karna**  
4️⃣ **🔎 Multi-Level Search Queries**  
5️⃣ **🚀 Practical Example with Output**  
6️⃣ **🛠️ Important Considerations**  

---

## 1️⃣ **🔍 Multi-Level Relations Kya Hote Hain?**  

Elasticsearch me **multi-level relations** ka use tab hota hai jab ek entity ke andar multiple related entities store karni ho.  

👉 Example:  
- **Company** ke multiple **departments** ho sakte hain.  
- **Department** ke multiple **employees** ho sakte hain.  
- **Company** ka ek **supplier** bhi ho sakta hai, jo department se directly linked nahi hai.  

Ye hierarchical relation **parent-child relation** ka ek extended form hai. **Elasticsearch me parent-child relation dene ke liye `join` data type ka use hota hai.**  

---

## 2️⃣ **🏗️ Index aur Mapping Setup**  

Pehle ek **index create karte hain**, jisme `join` field define karenge.  

### ✅ **Index Creation with Multi-Level Relations**
```json
PUT company
{
  "mappings": {
    "properties": {
      "name": { "type": "keyword" },
      "relation": {
        "type": "join",
        "relations": {
          "company": ["department", "supplier"], 
          "department": ["employee"]
        }
      }
    }
  }
}
```
### 📝 **Explanation**:  
- **`relation` field** -> Join field define karta hai.  
- **Company** ke **departments & suppliers** ho sakte hain.  
- **Department** ke andar **employees** ho sakte hain.  

---

## 3️⃣ **📄 Documents ko Insert Karna**  

Ab **Company, Department, Supplier, aur Employees** ke documents insert karte hain.

### ✅ **Company Insert**
```json
PUT company/_doc/1
{
  "name": "Tech Corp",
  "relation": {
    "name": "company"
  }
}
```

### ✅ **Department Insert (Parent: Company)**
```json
PUT company/_doc/2?routing=1
{
  "name": "Engineering",
  "relation": {
    "name": "department",
    "parent": 1
  }
}
```

### ✅ **Supplier Insert (Parent: Company)**
```json
PUT company/_doc/3?routing=1
{
  "name": "XYZ Suppliers",
  "relation": {
    "name": "supplier",
    "parent": 1
  }
}
```

### ✅ **Employee Insert (Parent: Department)**
```json
PUT company/_doc/4?routing=1
{
  "name": "John Doe",
  "relation": {
    "name": "employee",
    "parent": 2
  }
}
```
### 📝 **Explanation**:  
- **`routing=1`** -> Documents ko correct shard pe store karne ke liye **company ke ID** ka use kiya gaya hai.  
- **Department ka parent company (ID = 1) hai.**  
- **Employee ka parent department (ID = 2) hai.**  

---

## 4️⃣ **🔎 Multi-Level Search Queries**  

### 🔍 **Find Companies with a Specific Employee**
```json
GET company/_search
{
  "query": {
    "has_child": {
      "type": "department",
      "query": {
        "has_child": {
          "type": "employee",
          "query": {
            "term": { "name.keyword": "John Doe" }
          }
        }
      }
    }
  }
}
```
### ✅ **Output**
```json
{
  "hits": {
    "hits": [
      {
        "_id": "1",
        "_source": {
          "name": "Tech Corp"
        }
      }
    ]
  }
}
```
### 📝 **Explanation**:  
- **Pehle `has_child` query department dhoondh rahi hai.**  
- **Fir `has_child` query employee dhoondh rahi hai.**  
- **Agar John Doe kisi department me hai, to uska parent company return hoga.**  

---

## 5️⃣ **🚀 Practical Example with Output**  

Ab ek **real-world example** dekhte hain:

🔹 **Scenario**: Tumhe ek aise **company** ki details chahiye jisme **Marketing department ho aur wahan John Doe employee ho**.

```json
GET company/_search
{
  "query": {
    "has_child": {
      "type": "department",
      "query": {
        "bool": {
          "must": [
            { "term": { "name.keyword": "Marketing" } },
            {
              "has_child": {
                "type": "employee",
                "query": {
                  "term": { "name.keyword": "John Doe" }
                }
              }
            }
          ]
        }
      }
    }
  }
}
```

### ✅ **Expected Output**
```json
{
  "hits": {
    "total": 1,
    "hits": [
      {
        "_id": "1",
        "_source": {
          "name": "Tech Corp"
        }
      }
    ]
  }
}
```
### 📝 **Explanation**:
- **Sabse pehle department dhoondh rahe hain jiska naam Marketing hai.**  
- **Fir us department me employee dhoondh rahe hain jiska naam John Doe hai.**  
- **Agar match hota hai to uska parent company return hota hai.**  

---

## 6️⃣ **🛠️ Important Considerations**  

🔹 **Routing Query Parameter**  
  - Multi-level relations me **routing zaroori hota hai**.  
  - Query me hamesha **grandparent ID use karo** taaki document sahi shard me mile.  

🔹 **Nested Queries**  
  - Agar **multiple levels ka relation hai**, to queries ko **nest** karna padta hai jaise upar ki example me.  

🔹 **Performance Impact**  
  - Ye **join-based relation** hoti hai jo **Elasticsearch me slow ho sakti hai** agar bahut zyada data ho.  

---

## 🎯 **Conclusion**  

✅ **Multi-level relations Elasticsearch me complex hierarchical data ko efficiently store aur query karne ka ek powerful feature hai.**  
✅ **Parent-child relations ka use karke hum Company -> Department -> Employees ka ek structured relation maintain kar sakte hain.**  
✅ **Multi-level search queries me `has_child`, `has_parent`, aur `parent_id` ka sahi istemal karna padta hai.**  

