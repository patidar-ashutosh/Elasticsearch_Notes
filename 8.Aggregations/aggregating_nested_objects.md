## 🔥 **Aggregating Nested Objects in Elasticsearch**  

### **Nested Aggregations**  
Elasticsearch me **nested objects** normal objects ki tarah aggregate nahi ho sakte.  
Agar tumhe kisi **nested field** pe aggregation karni hai to **Nested Aggregation** ka use karna padega.  

💡 **Example:** Tumhare paas `departments` index hai jisme har department ke andar multiple `employees` hain.  
Agar tum **sabhi employees ka minimum age find karna chahte ho**, to simple `min` aggregation kaam nahi karegi.  
Uske liye hume **nested aggregation** lagani padegi.  

---

## 📌 **Table of Contents**  

1️⃣ [🔍 Nested Aggregation Kya Hai?](#1-nested-aggregation-kya-hai)  
2️⃣ [⚠️ Normal Aggregation Kyun Kaam Nahi Karti?](#2-normal-aggregation-kyun-kaam-nahi-karti)  
3️⃣ [📝 Example: Nested Aggregation](#3-example-nested-aggregation)  
4️⃣ [🔄 Combining Nested Aggregation with Other Aggregations](#4-combining-nested-aggregation-with-other-aggregations)  
5️⃣ [⚠️ Important Notes](#5-important-notes)  

---

## 1️⃣ 🔍 **Nested Aggregation Kya Hai?**  

Agar kisi field ka **data type `nested` hai**, to usko normal aggregation se process nahi kiya ja sakta.  
🔹 **Nested Aggregation** ka use karke tum **nested documents ko aggregate** kar sakte ho.  

🚀 **Nested Aggregation Steps:**  
✅ **Step 1:** Pehle ek **bucket** create hota hai jisme nested documents aate hain.  
✅ **Step 2:** Phir tum us bucket ke andar **sub-aggregations** laga sakte ho (jaise `min`, `sum`, `avg` etc.).  

---

## 2️⃣ ⚠️ **Normal Aggregation Kyun Kaam Nahi Karti?**  

Agar tum `employees.age` par bina nested aggregation ke **`min` aggregation** lagate ho, to **result null aayega**.  
🚨 **Reason:** Elasticsearch nested fields ko **independent documents** ki tarah store karta hai, to normal aggregations unko access nahi kar sakti.  

---

## 3️⃣ 📝 **Example: Nested Aggregation**  

### **Step 1: Sample Data Insert Karna**  

Pehle ek **`departments` index** create karte hain jisme nested `employees` field hogi.  

```json
PUT /departments
{
  "mappings": {
    "properties": {
      "name": { "type": "keyword" },
      "employees": {
        "type": "nested",
        "properties": {
          "name": { "type": "keyword" },
          "age": { "type": "integer" }
        }
      }
    }
  }
}
```

---

### **Step 2: Data Insert Karna**  

```json
POST /departments/_doc/1
{
  "name": "Engineering",
  "employees": [
    { "name": "Alice", "age": 25 },
    { "name": "Bob", "age": 30 }
  ]
}

POST /departments/_doc/2
{
  "name": "HR",
  "employees": [
    { "name": "Charlie", "age": 22 },
    { "name": "David", "age": 19 }
  ]
}
```

---

### **Step 3: Normal Aggregation (Wrong Approach)**  

Agar tum bina `nested` aggregation ke `min` apply karte ho:  

```json
{
  "aggs": {
    "min_employee_age": {
      "min": {
        "field": "employees.age"
      }
    }
  }
}
```

❌ **Output:**  

```json
{
  "aggregations": {
    "min_employee_age": {
      "value": null
    }
  }
}
```

🚨 **Problem:** Elasticsearch ko `employees.age` field mil hi nahi rahi kyunki yeh `nested` field hai.  

---

### **Step 4: Correct Query Using Nested Aggregation**  

Ab hum **Nested Aggregation** ka use karenge taaki `employees` field access ho sake.  

```json
{
  "aggs": {
    "employees_bucket": {
      "nested": {
        "path": "employees"
      },
      "aggs": {
        "min_employee_age": {
          "min": {
            "field": "employees.age"
          }
        }
      }
    }
  }
}
```

✅ **Yaha pe hum `nested` aggregation use kar rahe hain taaki `employees` bucket create ho sake.**  
✅ **Uske andar `min` aggregation laga rahe hain `employees.age` par.**  

---

### **Step 5: Expected Output**  

```json
{
  "aggregations": {
    "employees_bucket": {
      "doc_count": 4,
      "min_employee_age": {
        "value": 19
      }
    }
  }
}
```

✅ **`doc_count: 4` ka matlab hai ki total 4 employees nested bucket me aaye.**  
✅ **`min_employee_age: 19` ka matlab hai ki sabse chhoti age 19 hai.**  

---

## 4️⃣ 🔄 **Combining Nested Aggregation with Other Aggregations**  

Ab hum ek aur aggregation add karte hain jo **average age calculate karegi employees ki.**  

```json
{
  "aggs": {
    "employees_bucket": {
      "nested": {
        "path": "employees"
      },
      "aggs": {
        "min_employee_age": {
          "min": {
            "field": "employees.age"
          }
        },
        "avg_employee_age": {
          "avg": {
            "field": "employees.age"
          }
        }
      }
    }
  }
}
```

✅ **Is query me hum ek saath `min` aur `avg` aggregation laga rahe hain.**  

---

### **Expected Output**  

```json
{
  "aggregations": {
    "employees_bucket": {
      "doc_count": 4,
      "min_employee_age": {
        "value": 19
      },
      "avg_employee_age": {
        "value": 24
      }
    }
  }
}
```

✅ **Sabse chhoti age `19` hai.**  
✅ **Average age `24` hai.**  

---

## 5️⃣ ⚠️ **Important Notes**  

🔹 **Nested Aggregation sirf `nested` type ki fields pe lagti hai.**  
🔹 **Normal aggregation `nested` fields ko access nahi kar sakti.**  
🔹 **Sabse pehle ek `nested` bucket create hoti hai, phir uske andar aggregation lagti hai.**  
🔹 **Sub-aggregations ke saath kaafi powerful analysis ho sakti hai.**  

---

## 🎯 **Conclusion**  

✅ **Nested Aggregation** Elasticsearch me **nested objects** pe aggregation karne ka **sahi tareeka** hai.  
✅ **Agar field `nested` type ki hai, to bina nested aggregation ke us pe kaam nahi hoga.**  
✅ **Nested aggregation ke andar hum koi bhi metric aggregation laga sakte hain jaise `min`, `max`, `sum`, `avg` etc.**  

