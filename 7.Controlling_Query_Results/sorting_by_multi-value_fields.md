# 📌 Sorting by Multi-Value Fields in **Elasticsearch**  

## 📑 **Table of Contents**  

1️⃣ **[🔍 Introduction – Multi-Value Fields](#1)**  
2️⃣ **[📜 Sorting Modes Overview](#2)**  
3️⃣ **[🎯 Sorting by Average (`avg`)](#3)**  
4️⃣ **[➕ Sorting by Sum (`sum`)](#4)**  
5️⃣ **[📊 Sorting by Min & Max (`min` / `max`)](#5)**  
6️⃣ **[🗓 Sorting Dates with Multi-Value Fields](#6)**  
7️⃣ **[✅ Conclusion & Best Practices](#7)**  

---

## 🔍 **1. Introduction** – Multi-Value Fields  <a id="1"></a>

Kuch fields aisi hoti hain jo **single value ke bajay multiple values** store kar sakti hain. Example ke liye, **ratings** field jo ek **array of numbers** store karti hai.  

Elasticsearch by default **first value** ko consider karta hai sorting ke liye, **lekin** tum `mode` specify karke control kar sakte ho ki kaunsi value use karni hai.

📌 **Common Use Cases for Multi-Value Fields:**  
- **Ratings** (Multiple user ratings for a recipe)  
- **Prices** (Different price variations of a product)  
- **Timestamps** (Different dates when a document was updated)  

---

## 📜 **2. Sorting Modes Overview**  <a id="2"></a>

Multi-value fields ko sort karne ke liye **5 sorting modes** hote hain:  

| 🔢 **Mode**  | 📌 **Description** |
|-------------|-------------------|
| **`min`**  | **Sabse chhoti value** ko sorting ke liye use karega |
| **`max`**  | **Sabse badi value** ko sorting ke liye use karega |
| **`avg`**  | **Array ka average** calculate karega aur uske basis pe sort karega |
| **`sum`**  | **Saari values ka sum** karega aur sort karega |
| **`median`**  | **Middle value** ko sorting ke liye consider karega (less commonly used) |

---

## 🎯 **3. Sorting by Average (`avg`)**  <a id="3"></a>

Agar tum chahte ho ki **highest average rating** wale recipes pehle aayein, toh `avg` mode ka use kar sakte ho.

### ✅ **Query: Sorting by Average Rating (Descending)**
```json
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "ratings": {
        "order": "desc",
        "mode": "avg"
      }
    }
  ],
  "_source": ["title", "ratings"]
}
```
### 🔍 **Output Example:**
| 🏆 **Title**  | ⭐ **Ratings**  | 📊 **Avg Rating** |
|--------------|----------------|-------------------|
| **Recipe A** | [4, 5, 5]      | 4.67 ✅ |
| **Recipe B** | [3, 5]        | 4.00 |
| **Recipe C** | [2, 3, 4]      | 3.00 |

✅ **Sabse highest average rating wale results pehle dikh rahe hain.**  

---

## ➕ **4. Sorting by Sum (`sum`)**  <a id="4"></a>

Agar tum chahte ho ki **total rating sum** ke basis pe sorting ho, toh `sum` mode ka use kar sakte ho.

### ✅ **Query: Sorting by Sum of Ratings (Descending)**
```json
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "ratings": {
        "order": "desc",
        "mode": "sum"
      }
    }
  ],
  "_source": ["title", "ratings"]
}
```
### 🔍 **Output Example:**
| 🏆 **Title**  | ⭐ **Ratings**  | 🔢 **Total Sum** |
|--------------|----------------|------------------|
| **Recipe A** | [4, 5, 5]      | 14 ✅ |
| **Recipe B** | [3, 5]        | 8 |
| **Recipe C** | [2, 3, 4]      | 9 |

✅ **Jis recipe ke ratings ka sum zyada hai, wo pehle dikh rahi hai.**  

---

## 📊 **5. Sorting by Min & Max (`min` / `max`)**  <a id="5"></a>

Agar tum **sabse chhoti** ya **sabse badi** rating ke basis pe sorting karna chahte ho, toh `min` ya `max` use kar sakte ho.

### ✅ **Query: Sorting by Max Rating (Descending)**
```json
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "ratings": {
        "order": "desc",
        "mode": "max"
      }
    }
  ],
  "_source": ["title", "ratings"]
}
```
### 🔍 **Output Example:**
| 🏆 **Title**  | ⭐ **Ratings**  | 🔝 **Max Rating** |
|--------------|----------------|------------------|
| **Recipe A** | [4, 5, 5]      | 5 ✅ |
| **Recipe B** | [3, 5]        | 5 ✅ |
| **Recipe C** | [2, 3, 4]      | 4 |

**Jis recipe ki sabse highest rating zyada hai, wo pehle dikh rahi hai.**  

📌 **Similarly, tum `min` use kar sakte ho agar tumhe sabse chhoti rating wale pehle dikhane hain.**  

---

## 🗓 **6. Sorting Dates with Multi-Value Fields**  <a id="6"></a>

`min` aur `max` mode **numbers ke saath saath dates pe bhi kaam karta hai.**  

### ✅ **Query: Sorting by Earliest Created Date (Ascending)**
```json
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "created_dates": {
        "order": "asc",
        "mode": "min"
      }
    }
  ],
  "_source": ["title", "created_dates"]
}
```
### 🔍 **Output Example:**
| 🏆 **Title**  | 📅 **Created Dates**  | ⏳ **Earliest Date** |
|--------------|----------------------|---------------------|
| **Recipe A** | ["2023-05-01", "2023-06-15"] | 2023-05-01 ✅ |
| **Recipe B** | ["2023-07-10", "2023-08-01"] | 2023-07-10 |
| **Recipe C** | ["2023-09-05", "2023-10-10"] | 2023-09-05 |

✅ **Sabse purani date wale documents pehle dikh rahe hain.**  

---

## ✅ **7. Conclusion & Best Practices**  <a id="7"></a>

✔ **Multi-value fields Elasticsearch me by default first value ke basis pe sort hote hain.**  
✔ **`mode` parameter se tum control kar sakte ho ki kaunsi value sort ke liye use ho.**  
✔ **`avg`, `sum`, `min`, `max`, aur `median` modes ka use alag-alag scenarios me hota hai.**  
✔ **Dates ke liye `min` aur `max` modes kaafi useful hote hain.**  
✔ **Sorting ke liye field type float, integer ya date hona zaroori hai.**  

---

