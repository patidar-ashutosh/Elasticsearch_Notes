# 📌 **Prefixes, Wildcards & Regular Expressions**

## 📖 **Table of Contents**  

1️⃣ [🔍 Introduction](#1)  
2️⃣ [🔠 Prefix Query](#2)  
3️⃣ [🃏 Wildcard Query](#3)  
4️⃣ [🔢 Regular Expression Query](#4)  
5️⃣ [🎭 Lucene Regex Engine](#5)  
6️⃣ [🔠 Case Insensitive Searches](#6)  
7️⃣ [🏁 Conclusion](#7)  

---

## 1. 🔍 **Introduction** <a id="1"></a>

Term level queries sirf **exact matches** ke liye hoti hain, **lekin** kuch exceptions hain jo **thoda flexibility deti hain**:
- ✅ **Prefix query** → **Shuru ke characters match** karta hai.
- ✅ **Wildcard query** → **Flexible pattern matching** karta hai (`*`, `?`).
- ✅ **Regular expression query** → **Advanced pattern-based searching** allow karta hai.

Ye queries **keyword fields** ke saath use karni chahiye for better accuracy.

---

## 2. 🔠 **Prefix Query**  <a id="2"></a>

- Prefix query **field ke start se match karti hai**..
- Example: Agar hum `name.keyword` field pe **"Past"** se start hone wale values dhundhna chahein, toh yeh **"Pasta", "Pastry", "Paste"** ko match karegi.
- **Limitation**: Prefix **term ke start se match karega**, agar **word beech ya end me hai toh match nahi hoga**.

### 🔹 **Example**  
```json
{
  "query": {
    "prefix": {
      "name.keyword": {
        "value": "Past"
      }
    }
  }
}
```
### 🔹 **Expected Output**
```json
{
  "hits": {
    "hits": [
      { "_source": { "name": "Pasta" } },
      { "_source": { "name": "Pastry" } },
      { "_source": { "name": "Paste" } }
    ]
  }
}
```
🔹 **Limitation**:
- Agar **"Pasta"** kisi product name ke end me ho, toh **match nahi karega**.

👉 **Better Approach**:
- Agar **word kahin bhi ho sakta hai**, toh **tags field ya full-text queries use karo**.

---

## 3. 🃏 **Wildcard Query**  <a id="3"></a>

Wildcard query **2 tarike ke wildcards support karti hai**:
1. **`?` (Question Mark)** - **Ek single character match karega**  
2. **`*` (Asterisk)** - **Zero ya more characters match karega**  

### 🔹 **Example 1: Question Mark (`?`)**
```json
{
  "query": {
    "wildcard": {
      "name.keyword": {
        "value": "P?sta"
      }
    }
  }
}
```
### 🔹 **Expected Output**
```json
{
  "hits": {
    "hits": [
      { "_source": { "name": "Pasta" } },
      { "_source": { "name": "Pista" } }
    ]
  }
}
```
---

### 🔹 **Example 2: Asterisk (`*`)**
```json
{
  "query": {
    "wildcard": {
      "name.keyword": {
        "value": "Be*t"
      }
    }
  }
}
```
### 🔹 **Expected Output**
```json
{
  "hits": {
    "hits": [
      { "_source": { "name": "Beet" } },
      { "_source": { "name": "Beat" } },
      { "_source": { "name": "Beef" } }
    ]
  }
}
```
⚠️ **Performance Note**:
- **Start me wildcard use karna slow kar sakta hai**, toh avoid karo agar possible ho.

---

## 4. 🔢 **Regular Expression Query**  <a id="4"></a>

Regular expressions **wildcards se zyada powerful** hoti hain.  

### 🔹 **Example 1: Simple Regex**
```json
{
  "query": {
    "regexp": {
      "name.keyword": {
        "value": "Bee(f|r)+"
      }
    }
  }
}
```
### 🔹 **Expected Output**
```json
{
  "hits": {
    "hits": [
      { "_source": { "name": "Beef" } },
      { "_source": { "name": "Beer" } }
    ]
  }
}
```
---

### 🔹 **Example 2: Flexible Matching**
```json
{
  "query": {
    "regexp": {
      "name.keyword": {
        "value": "Bee[a-z]+"
      }
    }
  }
}
```
### 🔹 **Expected Output**
```json
{
  "hits": {
    "hits": [
      { "_source": { "name": "Beet" } },
      { "_source": { "name": "Beef" } },
      { "_source": { "name": "Beer" } }
    ]
  }
}
```
Regex queries se **complex patterns** define kar sakte ho, jaise:
- **Dynamic patterns**: `"Bee[a-z]+"` (Bee ke baad koi bhi letter ho)
- **Wildcard use**: `"Beer.*"` (Beer ke baad kuch bhi ho)

🔹 **Common Mistake**:  
- **Full word match hona chahiye**. `"Bee"` wale regex ko `"Beetroot"` match nahi karega.

---

## 5. 🎭 **Lucene Regex Engine** <a id="5"></a>

Elasticsearch **Apache Lucene Regex Engine** use karta hai, jo **standard regex engines se thoda different hota hai**:
- **`^` aur `$` anchors supported nahi hote**.
- **Pura term match karna zaroori hota hai**.
- **Lucene syntax thoda different ho sakta hai**, toh standard regex ka **conversion zaroori** ho sakta hai.

---

## 6. 🔠 **Case Insensitive Searches**  <a id="6"></a>

By default, **yeh queries case-sensitive hoti hain**.  
Agar **case ignore karna hai**, toh **`case_insensitive: true`** parameter add kar sakte ho:

### 🔹 **Example**
```json
{
  "query": {
    "prefix": {
      "name.keyword": {
        "value": "past",
        "case_insensitive": true
      }
    }
  }
}
```
### 🔹 **Expected Output**
```json
{
  "hits": {
    "hits": [
      { "_source": { "name": "PASTA" } },
      { "_source": { "name": "pasta" } },
      { "_source": { "name": "Pastel" } }
    ]
  }
}
```
Isse **"past", "Past", "PAST"** sab match ho jayenge.

---

## 7. 🏁 **Conclusion** <a id="7"></a>

| Query Type | Description | Example |
|------------|------------|---------|
| **Prefix Query** | Term ke start se match karta hai | `"Past"` → `"Pasta", "Pastry"` |
| **Wildcard Query** | `*` (zero or more), `?` (single char) | `"Be*t"` → `"Beet", "Beef"` |
| **Regex Query** | Complex pattern matching | `"Bee(f|r)+"` → `"Beef", "Beer"` |
| **Case Insensitive** | Case ignore karne ke liye | `"past"` → `"Past", "PAST"` |

### ✅ **Key Takeaways**:
1. **Prefix Query** sirf **start se match karta hai**.
2. **Wildcard Query** `?` aur `*` support karta hai, **lekin performance slow ho sakti hai**.
3. **Regular Expressions** **advanced matching** allow karti hain, lekin **Lucene syntax rules follow karni padti hain**.
4. **Case insensitive search enable kar sakte ho** using `case_insensitive: true`.

