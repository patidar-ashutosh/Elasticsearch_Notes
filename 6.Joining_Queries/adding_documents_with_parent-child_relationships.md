## 📜 **Table of Contents**  

1️⃣ **[Documents ko Indexing Karna](#1-documents-ko-indexing-karna) 📄**  
2️⃣ **[Parent-Child Relationship Kya Hai?](#2-parent-child-relationship-kya-hai) 👨‍👩‍👦**  
3️⃣ **[Join Field Ka Use Karke Parent Ko Add Karna](#3-join-field-ka-use-karke-parent-ko-add-karna) 🔗**  
4️⃣ **[Child Documents Ko Parent Se Link Karna](#4-child-documents-ko-parent-se-link-karna) 🧑‍💼➡️🏢**  
5️⃣ **[Routing Parameter Ka Role](#5-routing-parameter-ka-role) 📌**  
6️⃣ **[Dynamic Mapping Ka Use](#6-dynamic-mapping-ka-use) 🏗️**  
7️⃣ **[Complete Example with Expected Output](#7-complete-example-with-expected-output) 💻**  

---  

## 1️⃣ **Documents ko Indexing Karna** 📄  

Elasticsearch me documents JSON format me store hote hain, aur unhe **index** ke andar add karte hain. Basic tarika yeh hai:  

```json
PUT my_index/_doc/1
{
  "name": "John Doe",
  "age": 30,
  "gender": "M"
}
```

Yeh ek simple document add karega **my_index** ke andar.  

---  

## 2️⃣ **Parent-Child Relationship Kya Hai?** 👨‍👩‍👦  

Kayi baar documents ke beech **relation** hota hai. Jaise:  

- **Department (Parent)** ➝ **Employee (Child)**  
- **Company (Parent)** ➝ **Branch (Child)**  

Is case me, **department** parent hai aur **employee** uska child. Matlab har employee ek specific department me kaam karega.  

Normal relational DB me **foreign keys** hoti hain, but Elasticsearch me **join fields** ka use hota hai.  

---  

## 3️⃣ **Join Field Ka Use Karke Parent Ko Add Karna** 🔗  

Jab hum **department (parent document)** ko add karte hain, to hume **join field** specify karni hoti hai.  

```json
PUT my_index/_doc/1
{
  "name": "Engineering",
  "join_field": "department"
}
```

🔹 **join_field** ek arbitrary naam hai, jo humne mapping ke andar define kiya tha. Iska bas **mapping se match** karna zaroori hai.  

---  

## 4️⃣ **Child Documents Ko Parent Se Link Karna** 🧑‍💼➡️🏢  

Ab ek **employee (child document)** ko add karte hain aur specify karte hain ki **parent ID** kaunsa hoga.  

```json
PUT my_index/_doc/2?routing=1
{
  "name": "Alice",
  "age": 25,
  "gender": "F",
  "join_field": {
    "name": "employee",
    "parent": 1
  }
}
```

🔹 **join_field ek object ban gaya hai** kyunki ye ab ek child document hai.  
🔹 **"parent": 1** batata hai ki **ye employee department 1 ka hissa hai**.  

---  

## 5️⃣ **Routing Parameter Ka Role** 📌  

🚨 Jab parent-child relation use kar rahe ho, to **routing** zaroori hoti hai!  

❌ Agar hum bina routing ke child document add karein, to error aayega:  

```
"reason": "Routing is required for [my_index]/[2]"
```

✔️ Solution: Parent ka ID specify karo **routing parameter me**.  

```json
PUT my_index/_doc/3?routing=1
{
  "name": "Bob",
  "age": 28,
  "gender": "M",
  "join_field": {
    "name": "employee",
    "parent": 1
  }
}
```

🔹 Routing ensures ki **parent aur child ek hi shard me store ho**.  

---  

## 6️⃣ **Dynamic Mapping Ka Use** 🏗️  

💡 Elasticsearch me **fields ko manually define** karna zaroori nahi hai! Agar hum ek naya field (e.g. salary) add kar dein, to Elasticsearch automatically mapping create kar lega.  

```json
PUT my_index/_doc/4?routing=1
{
  "name": "Charlie",
  "age": 35,
  "gender": "M",
  "salary": 50000,
  "join_field": {
    "name": "employee",
    "parent": 1
  }
}
```

Yeh Elasticsearch ka **Dynamic Mapping** feature hai jo naye fields ko automatically detect karta hai.  

---  

## 7️⃣ **Complete Example with Expected Output** 💻  

### ✅ **Parent Document Add Karna (Department)**  

```json
PUT my_index/_doc/1
{
  "name": "HR",
  "join_field": "department"
}
```

🟢 **Output:**  

```json
{
  "_index": "my_index",
  "_id": "1",
  "result": "created"
}
```

### ✅ **Child Document Add Karna (Employee)**  

```json
PUT my_index/_doc/2?routing=1
{
  "name": "John Doe",
  "age": 30,
  "gender": "M",
  "join_field": {
    "name": "employee",
    "parent": 1
  }
}
```

🟢 **Output:**  

```json
{
  "_index": "my_index",
  "_id": "2",
  "result": "created"
}
```

### ✅ **Agar Routing Parameter Nahi Diya to Error**  

```json
PUT my_index/_doc/3
{
  "name": "Jane Doe",
  "age": 28,
  "gender": "F",
  "join_field": {
    "name": "employee",
    "parent": 1
  }
}
```

🔴 **Error:**  

```json
{
  "error": {
    "reason": "Routing is required for [my_index]/[3]"
  }
}
```

🚀 **Solution:** `?routing=1` parameter add karo!  

---  

## 🎯 **Key Takeaways**  

✅ **Join fields** ka use karke **parent-child relation** establish kar sakte hain.  
✅ Parent document simple hota hai, but **child document me "parent" ID specify** karni hoti hai.  
✅ **Routing zaroori hoti hai** taaki parent-child ek hi shard me store ho.  
✅ **Dynamic mapping** se naye fields automatically detect ho jate hain.  

---  
