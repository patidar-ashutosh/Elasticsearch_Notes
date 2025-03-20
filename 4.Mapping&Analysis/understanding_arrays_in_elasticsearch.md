### 📖 **Understanding Arrays in Elasticsearch**  

Elasticsearch me arrays ko handle karne ka tareeka thoda alag hai. Yahan pe **array data type exist nahi karta**, magar fir bhi hum bina kisi special configuration ke arrays ko store aur query kar sakte hain. Is guide me hum arrays ko deeply samjhenge aur unke working ka structure dekhenge.  

---

## 📌 **Table of Contents**
1️⃣ [Elasticsearch me Arrays Kya Hote Hain?](#1)  
2️⃣ [Arrays Kaise Kaam Karte Hain?](#2)  
3️⃣ [Arrays Ka Internal Storage](#3)  
4️⃣ [Array Ke Rules aur Constraints](#4)  
5️⃣ [Mixed Data Types in Arrays](#5)  
6️⃣ [Nested Arrays aur Objects](#6)  
7️⃣ [Summary](#7)  

---

## 1️⃣ **Elasticsearch me Arrays Kya Hote Hain?** <a id="1"></a>  

🔹 Elasticsearch me **array** ek alag se data type nahi hai.  
🔹 **Har field by default ek array ho sakti hai**, bina kisi extra configuration ke.  
🔹 Matlab ek field ek ya ek se zyada values store kar sakti hai.  

### ✅ **Example: Single aur Multiple Values**
```json
PUT my_index/_doc/1
{
  "tags": "elasticsearch"
}
```
```json
PUT my_index/_doc/2
{
  "tags": ["elasticsearch", "search", "database"]
}
```
🔹 Dono documents valid hain, aur Elasticsearch automatically `"tags"` ko array ki tarah treat karega.  

---

## 2️⃣ **Arrays Kaise Kaam Karte Hain?** <a id="2"></a>  

🔹 Elasticsearch automatically ek field ke liye **multiple values allow karta hai** bina kisi special configuration ke.  
🔹 **Dynamic Mapping** ke wajah se, agar hum ek field me pehle se hi array insert kar dete hain, toh Elasticsearch use automatically **"text"** ya **"keyword"** data type assign karega.  

### ✅ **Example: Dynamic Mapping**
```json
PUT my_index/_doc/3
{
  "colors": ["red", "blue", "green"]
}
```
🔹 **Elasticsearch isko automatically text field ki tarah treat karega.**  
🔹 Koi bhi array define karne ki zaroorat nahi hai.  

---

## 3️⃣ **Arrays Ka Internal Storage** <a id="3"></a>  

🔹 Elasticsearch internally **text fields** ko ek single string me merge karta hai.  
🔹 **Analyze API** ka use karke hum dekh sakte hain ki values kaise store ho rahi hain.  

### ✅ **Example: Analyze API**
```json
GET _analyze
{
  "tokenizer": "standard",
  "text": ["search", "engine"]
}
```
👀 **Output:**  
```json
{
  "tokens": [
    { "token": "search", "start_offset": 0, "end_offset": 6 },
    { "token": "engine", "start_offset": 7, "end_offset": 13 }
  ]
}
```
🔹 **Notice karo ki "engine" start_offset 7 se ho raha hai**, iska matlab hai ki Elasticsearch values ko ek single string ki tarah treat karta hai.  

---

## 4️⃣ **Array Ke Rules aur Constraints** <a id="4"></a>  

✅ **Allowed:**  
✔ Arrays me **same data type** ki multiple values ho sakti hain.  
✔ Arrays ke elements ko **query** karna possible hai.  

❌ **Not Allowed:**  
🚫 **Different data types** ka mix allowed nahi hai (except coercion cases).  
🚫 **Arrays me nested arrays** automatically flatten ho jate hain.  

### ❌ **Example: Mixed Data Types (Invalid)**
```json
PUT my_index/_doc/4
{
  "numbers": [1, "two", 3]
}
```
❌ **Elasticsearch error dega** kyunki ek integer aur ek string ek hi array me store nahi ho sakte.  

---

## 5️⃣ **Mixed Data Types in Arrays** <a id="5"></a>  

🔹 Kabhi kabhi **coercion** enabled hota hai, jisme Elasticsearch automatically kuch types ko convert kar sakta hai.  
🔹 **Agar mapping pehle se exist karti hai**, toh kuch mixed data types allowed ho sakte hain.  

### ✅ **Example: Coercion Allowed**
```json
PUT my_index
{
  "mappings": {
    "properties": {
      "prices": { "type": "float" }
    }
  }
}
```
```json
PUT my_index/_doc/5
{
  "prices": [9.99, "10.5", 15]
}
```
🟢 **Elasticsearch `"10.5"` ko float me convert kar dega aur document store ho jayega.**  

🚨 **Magar agar mapping nahi hai, toh coercion allowed nahi hoga!**  

---

## 6️⃣ **Nested Arrays aur Objects** <a id="6"></a>  

🔹 **Elasticsearch nested arrays ko support nahi karta**, aur automatically **flatten** kar deta hai.  
🔹 **Agar aapko nested objects query karne hain, toh "nested" type use karna padega.**  

### ❌ **Example: Nested Array (Flattened)**
```json
PUT my_index/_doc/6
{
  "values": [[1, 2], [3, 4]]
}
```
👀 **Stored As:**  
```json
{
  "values": [1, 2, 3, 4]
}
```
❌ **Nested structure ko maintain nahi kiya jayega.**  

✅ **Solution: "nested" Type Use Karo**
```json
PUT my_index
{
  "mappings": {
    "properties": {
      "users": {
        "type": "nested"
      }
    }
  }
}
```
```json
PUT my_index/_doc/7
{
  "users": [
    { "name": "Amit", "age": 30 },
    { "name": "Rahul", "age": 25 }
  ]
}
```
🔹 **Ab Elasticsearch nested objects ko alag alag treat karega.**  

---

## 7️⃣ **Summary** <a id="7"></a>  

🔹 **Elasticsearch me arrays ek separate data type nahi hote, magar har field automatically ek array ban sakti hai.**  
🔹 **Text fields ko internally ek single string me convert kar diya jata hai.**  
🔹 **Arrays ke andar sirf same data type ki values honi chahiye.**  
🔹 **Coercion kabhi kabhi allow hota hai agar mapping pehle se exist karti ho.**  
🔹 **Nested arrays automatically flatten ho jate hain, magar "nested" type use karke objects ko maintain kiya ja sakta hai.**  

