## 📑 Table of Contents  

1️⃣ **Mapping me Relationship Define Karna** 🗺️  
2️⃣ **Join Field Kya Hota Hai?** 🔗  
3️⃣ **Relations Object Ka Role** 🏗️  
4️⃣ **Example: Department aur Employee Relationship** 🏢👨‍💼  
5️⃣ **Multiple Children Relationship** 👨‍👩‍👧‍👦  
6️⃣ **Querying Parent-Child Relationships** 🔍  

---

## 1️⃣ **Mapping me Relationship Define Karna** 🗺️  
Elasticsearch me **documents ka relationship** define karne ke liye hum **mapping** ka use karte hain. Relationship define karna **important** hota hai jab hume **parent-child structure** create karna ho jaise:  
- **Department → Employee**  
- **Country → State → City**  
- **Blog → Comments**  

Iske liye Elasticsearch ek **special field** provide karta hai **join field**.  

---

## 2️⃣ **Join Field Kya Hota Hai?** 🔗  
🔹 Join Field ek **special data type** hota hai jo documents ke **relations** ko define karta hai.  
🔹 Ye **parent-child** structure banane me help karta hai bina **multiple indices** create kiye.  
🔹 **Parent aur Child ka relation** ek **single index** me manage ho sakta hai.  

📌 **Syntax:**  
```json
{
  "mappings": {
    "properties": {
      "join_field": {
        "type": "join",
        "relations": {
          "parent": "child"
        }
      }
    }
  }
}
```

💡 **Note:**  
- `"parent": "child"` ka matlab hai **parent document** `"parent"` hoga aur **child document** `"child"` hoga.  
- Isme **multiple children bhi ho sakte hain**, bas ek **array** me define karna hoga.  

---

## 3️⃣ **Relations Object Ka Role** 🏗️  
**Relations object** ke andar **key-value pairs** hote hain jo **document types ka relationship** define karte hain.  

📌 **Example:**  
```json
"relations": {
  "department": "employee"
}
```
Matlab **department parent** hai aur **employee child** hai.  

Agar **ek parent ke multiple children** hote, toh hum **array** ka use karte:  
```json
"relations": {
  "company": ["manager", "developer", "designer"]
}
```
Iska matlab **company parent** hai aur uske **3 children (manager, developer, designer)** hain.  

---

## 4️⃣ **Example: Department aur Employee Relationship** 🏢👨‍💼  
Chalo ek **proper mapping** banate hain **department aur employee** ke relation ke liye.  

### ✅ **Mapping Definition**  
```json
PUT company_index
{
  "mappings": {
    "properties": {
      "join_field": {
        "type": "join",
        "relations": {
          "department": "employee"
        }
      }
    }
  }
}
```

🔹 **`join_field`** me `type: "join"` hota hai.  
🔹 `"department": "employee"` relation define karta hai.  

---

## 5️⃣ **Multiple Children Relationship** 👨‍👩‍👧‍👦  
Agar ek **parent ke multiple children** hon toh usko **array me define** karte hain.  

📌 **Example:**  
```json
PUT school_index
{
  "mappings": {
    "properties": {
      "join_field": {
        "type": "join",
        "relations": {
          "school": ["teacher", "student"]
        }
      }
    }
  }
}
```
Iska matlab **school parent hai** aur uske **2 children (teacher & student)** hain.  

---

## 6️⃣ **Querying Parent-Child Relationships** 🔍  
Parent-child relationship ka **biggest advantage** ye hai ki hum **parent ke basis pe child** ya **child ke basis pe parent** ko query kar sakte hain.  

### ✅ **Parent Document Insert Karna**  
```json
POST company_index/_doc/1?refresh
{
  "name": "HR Department",
  "join_field": "department"
}
```
💡 **Yaha department ek parent hai** jiska naam "HR Department" hai.  

### ✅ **Child Document Insert Karna**  
```json
POST company_index/_doc/2?refresh
{
  "name": "John Doe",
  "join_field": {
    "name": "employee",
    "parent": "1"
  }
}
```
💡 **Yaha employee ek child hai** aur uska **parent department ID `1` hai**.  

### ✅ **Query: Get All Employees of a Department**  
```json
GET company_index/_search
{
  "query": {
    "has_parent": {
      "parent_type": "department",
      "query": {
        "match": { "name": "HR Department" }
      }
    }
  }
}
```
🔍 **Yeh query sare employees ko fetch karegi jo HR Department me kaam karte hain.**  

### ✅ **Query: Get Department of a Specific Employee**  
```json
GET company_index/_search
{
  "query": {
    "has_child": {
      "type": "employee",
      "query": {
        "match": { "name": "John Doe" }
      }
    }
  }
}
```
🔍 **Yeh query John Doe ka parent department return karegi.**  

---

## 🎯 **Conclusion**  
- 🔗 **Join Field** Elasticsearch me **document relationships** define karne ke liye use hota hai.  
- 🏗 **Relations Object** me parent-child ka structure define hota hai.  
- 🏢👨‍💼 **Example:** Department → Employee relationship define kar sakte hain.  
- 🔍 **Querying possible hai:** Parent ke basis pe child, ya child ke basis pe parent fetch kar sakte hain.  
- 🚀 **Efficient searching** ke liye yeh powerful feature hai **bina multiple indices create kiye.**  

