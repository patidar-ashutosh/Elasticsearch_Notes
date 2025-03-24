# Combining Explicit and Dynamic Mapping in Elasticsearch

## **Table of Contents**  

1️⃣ [Introduction](#1)  
2️⃣ [What is Explicit Mapping?](#2)  
3️⃣ [What is Dynamic Mapping?](#3)  
4️⃣ [Combining Explicit and Dynamic Mapping](#4)  
5️⃣ [Example Scenario](#5)  
6️⃣ [Benefits of Combining Both Approaches](#6)  
7️⃣ [Best Practices](#7)  
8️⃣ [Conclusion](#8)  

---

## 1. Introduction <a id="1"></a>

Ab tak tum explicit mapping aur dynamic mapping dono samajh chuke ho. Lekin ek bada sawal yeh hai ki **kis approach ko use karna chahiye?** Kya sirf ek ko use karein ya dono ka combination possible hai?

Good news yeh hai ki **explicit aur dynamic mapping ek saath use ho sakti hai**! Dono approaches ek doosre ko complement karti hain, aur hum kisi ek ko chhodne ki zaroorat nahi hai.

Is article me hum dekhenge ki kaise **explicit mapping aur dynamic mapping ko combine karke best indexing strategy** bana sakte hain.

---

## 2. What is Explicit Mapping? <a id="2"></a>

Explicit mapping ka matlab hota hai ki **hum khud Elasticsearch ko bata dete hain ki kaunse fields kis type ke hone chahiye**. Iska fayda yeh hai ki hum apni index structure ko **control** kar sakte hain.

**Example:**
```json
PUT people
{
  "mappings": {
    "properties": {
      "first_name": { "type": "text" }
    }
  }
}
```
Yeh mapping define karti hai ki **first_name** field ka type **text** hoga.

---

## 3. What is Dynamic Mapping? <a id="3"></a>

Dynamic mapping ka matlab hai ki **Elasticsearch khud fields detect karke unka type set kar deta hai**. Agar koi field mapping me nahi di gayi hai, toh Elasticsearch automatically ek mapping generate kar leta hai.

**Example:**
```json
POST people/_doc/1
{
  "first_name": "John",
  "last_name": "Doe"
}
```
Yaha **last_name** field ka koi mapping define nahi kiya gaya tha, toh Elasticsearch ne automatically ek mapping create kar di.

---

## 4. Combining Explicit and Dynamic Mapping <a id="4"></a>

Agar hum ek index me **explicit mapping** define karte hain, lekin kuch naye fields **dynamic mapping** se create hone dete hain, toh dono approaches ek saath kaam karti hain.

**Example:**

```json
PUT people
{
  "mappings": {
    "properties": {
      "first_name": { "type": "text" }
    }
  }
}
```

Ab agar hum ek document insert karein:

```json
POST people/_doc/1
{
  "first_name": "John",
  "last_name": "Doe"
}
```

Toh Elasticsearch **last_name** ke liye automatically ek mapping generate karega, kyunki yeh pehle define nahi thi.

Ab agar hum **mapping retrieve** karein:
```json
GET people/_mapping
```
Toh response me hume **first_name** explicitly mapped milega aur **last_name** dynamically mapped.

---

## 5. Example Scenario <a id="5"></a>

### Step 1: Index Creation with Explicit Mapping
```json
PUT employees
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "salary": { "type": "integer" }
    }
  }
}
```

### Step 2: Indexing a Document with Extra Fields
```json
POST employees/_doc/1
{
  "name": "Alice",
  "salary": 50000,
  "department": "IT"
}
```

### Step 3: Checking Mapping
```json
GET employees/_mapping
```
Output me **name** aur **salary** toh **explicit** mapping ke basis pe set honge, lekin **department** field automatically **dynamic mapping** ke through add ho jayegi.

---

## 6. Benefits of Combining Both Approaches <a id="6"></a>

1. **Flexibility** - Tum important fields ka type khud define kar sakte ho aur naye fields automatically detect ho sakte hain.
2. **Data Integrity** - Critical fields (jaise ki salary, date fields) ka type predefined hota hai, jisse incorrect data entry se bacha ja sakta hai.
3. **Scalability** - Jab naye attributes dynamically add hote hain, toh manually mapping update karne ki zaroorat nahi hoti.
4. **Performance Optimization** - Un fields ka explicit mapping define karke jo frequently query ki jati hain, tum indexing aur retrieval performance better bana sakte ho.

---

## 7. Best Practices <a id="7"></a>

1. **Important fields ka explicit mapping define karo** - Jisme predefined constraints ya optimized indexing ki zaroorat ho.
2. **Dynamic mapping ko controlled rakho** - Agar tum unwanted fields avoid karna chahte ho, toh dynamic mapping rules use karo.
3. **Index templates use karo** - Large scale systems me **index templates** ka use karke mapping ko standardize karo.
4. **Mapping inspect karo** - Time to time `GET _mapping` query run karke check karo ki Elasticsearch automatically galat data types toh detect nahi kar raha.
5. **Disable dynamic mapping (agar zaroorat ho)** - Agar tumhe pura control chahiye toh **dynamic mapping disable** bhi kar sakte ho.
```json
PUT my_index
{
  "mappings": {
    "dynamic": "strict"
  }
}
```

---

## 8. Conclusion <a id="8"></a>

Explicit aur dynamic mapping ko **combine karke best results achieve kiye ja sakte hain**. Tum **important fields** ka mapping manually define kar sakte ho aur naye fields Elasticsearch par chhod sakte ho.

Agar tum ek controlled indexing approach chahte ho jisme flexibility bhi ho, toh yeh combination **best practice** hai. Lekin agar tumhe **full control** chahiye toh dynamic mapping disable karna ek achha option ho sakta hai.

