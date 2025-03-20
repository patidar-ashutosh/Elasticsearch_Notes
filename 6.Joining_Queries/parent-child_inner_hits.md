# 📌 Parent/Child Inner Hits – Deep Explanation  

## 📖 Table of Contents  

1️⃣ **🔍 Parent-Child Relationship Kya Hoti Hai?**  
2️⃣ **📌 Inner Hits Kya Hai Aur Kyun Zaroori Hai?**  
3️⃣ **🔄 has_child Query Ke Saath Inner Hits**  
4️⃣ **🔗 has_parent Query Ke Saath Inner Hits**  
5️⃣ **🛠️ Practical Example with JSON Queries**  
6️⃣ **📊 Expected Output Explanation**  
7️⃣ **💡 Real-World Use Cases**  
8️⃣ **🔚 Summary & Key Takeaways**  

---  

## 1️⃣ 🔍 Parent-Child Relationship Kya Hoti Hai?  

**Parent/Child relationship** ka use tab hota hai jab tum **1-to-Many relationships** ko Elasticsearch me efficiently store aur query karna chahte ho.  

### **Example:**  
Ek **company** ka structure lo jisme **departments** hote hain aur unke andar multiple **employees** hote hain. Yaha:  

- **Department → Parent** (e.g., "Development", "HR")  
- **Employee → Child** (e.g., "Daniel Harris", "Emma Watson")  

Parent-child mapping ka fayda ye hai ki **documents ko independently index** kiya ja sakta hai bina **reindexing ki problem** aaye.  

---  

## 2️⃣ 📌 Inner Hits Kya Hai Aur Kyun Zaroori Hai?  

Jab tum **has_child** ya **has_parent** query run karte ho, to query **match hone wale documents ko return karti hai**, lekin **kis wajah se match hua**, ye dekhne ke liye **inner_hits** use kiya jata hai.  

**Inner Hits** ka use:  
✅ Query ke result me bata sakte ho ki **kaunsa child document parent ko match kar raha hai**  
✅ Ya **kaunsa parent document kisi child document ko match kar raha hai**  

---  

## 3️⃣ 🔄 has_child Query Ke Saath Inner Hits  

**has_child** query ka use hota hai jab tumhe **parent documents fetch karne ho jo kisi specific child document se match ho rahe ho.**  

### **Example Query:**  

```json
{
  "query": {
    "has_child": {
      "type": "employee",
      "query": {
        "match": { "name": "Daniel Harris" }
      },
      "inner_hits": {}
    }
  }
}
```

### **Explanation:**  
- Yaha hum **department documents** ko fetch kar rahe hain jisme **"Daniel Harris" naam ka employee (child document)** exist karta hai.  
- **inner_hits** option ke wajah se, hume **match hone wale employees ki details bhi milengi.**  

---  

## 4️⃣ 🔗 has_parent Query Ke Saath Inner Hits  

**has_parent** query ka use tab hota hai jab tumhe **child documents fetch karne ho jo kisi specific parent document se match ho rahe ho.**  

### **Example Query:**  

```json
{
  "query": {
    "has_parent": {
      "parent_type": "department",
      "query": {
        "match": { "name": "Development" }
      },
      "inner_hits": {}
    }
  }
}
```

### **Explanation:**  
- Yaha hum **employees (child documents)** ko fetch kar rahe hain jo **"Development" department (parent document)** ke andar aate hain.  
- **inner_hits** ki wajah se, hume **match hone wale parent ki details bhi milengi.**  

---  

## 5️⃣ 🛠️ Practical Example with JSON Queries  

### **Index Mapping:**  
```json
PUT company
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "join_field": {
        "type": "join",
        "relations": { "department": "employee" }
      }
    }
  }
}
```

### **Index Some Data:**  
```json
POST company/_doc/1?refresh
{
  "name": "Development",
  "join_field": "department"
}

POST company/_doc/2?refresh
{
  "name": "Daniel Harris",
  "join_field": { "name": "employee", "parent": "1" }
}
```

### **Run has_child Query:**  
```json
GET company/_search
{
  "query": {
    "has_child": {
      "type": "employee",
      "query": { "match": { "name": "Daniel Harris" } },
      "inner_hits": {}
    }
  }
}
```

### **Run has_parent Query:**  
```json
GET company/_search
{
  "query": {
    "has_parent": {
      "parent_type": "department",
      "query": { "match": { "name": "Development" } },
      "inner_hits": {}
    }
  }
}
```

---  

## 6️⃣ 📊 Expected Output Explanation  

Agar **has_child query** run karte ho, to output kuch aisa hoga:  

```json
{
  "hits": [
    {
      "_source": { "name": "Development" },
      "inner_hits": {
        "employee": {
          "hits": [
            { "_source": { "name": "Daniel Harris" } }
          ]
        }
      }
    }
  ]
}
```

👉 **Development department ke andar "Daniel Harris" employee mila, jo match karta hai.**  

Agar **has_parent query** run karte ho, to output kuch aisa hoga:  

```json
{
  "hits": [
    {
      "_source": { "name": "Daniel Harris" },
      "inner_hits": {
        "department": {
          "hits": [
            { "_source": { "name": "Development" } }
          ]
        }
      }
    }
  ]
}
```

👉 **"Daniel Harris" employee ka parent "Development" department hai.**  

---  

## 7️⃣ 💡 Real-World Use Cases  

✅ **E-commerce Applications:**  
   - Parent: **Product Category**  
   - Child: **Products**  
   - Query: Kaunse **products kis category ke andar match kar rahe hain?**  

✅ **Job Portals:**  
   - Parent: **Company**  
   - Child: **Job Listings**  
   - Query: Kis **company ke andar kaunsi jobs match ho rahi hain?**  

✅ **Social Media Platforms:**  
   - Parent: **Posts**  
   - Child: **Comments**  
   - Query: Kis **post ke andar kaunse comments relevant hain?**  

---  

## 8️⃣ 🔚 Summary & Key Takeaways  

✅ **has_child** query se **parent documents ko fetch** kar sakte ho jisme **specific child document match hota ho.**  
✅ **has_parent** query se **child documents ko fetch** kar sakte ho jo **specific parent document se match hote hain.**  
✅ **inner_hits** ka use karke hum **query match hone wale exact child/parent documents bhi dekh sakte hain.**  
✅ **Real-world use cases me ye approach bahut powerful hoti hai**, specially jab **parent-child relationships ka data store karna ho.**  

---  

