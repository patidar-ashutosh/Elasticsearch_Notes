# Script-Based Custom Sorting

## 📘 Table of Contents

1. 🔥 [Script Sort kya hota hai?](#1)  
2. 🧠 [Use Case — Kyun aur Kab chahiye?](#2)  
3. ⚙️ [Painless Script ka Syntax](#3)  
4. 🧪 [Line-by-Line Breakdown](#4)  
5. ✅ [Real-world Custom Order Example](#5)  
6. 💡 [Bonus: Tips & Summary](#6)  

---

## 1. 🔥 **Script Sort kya hota hai?** <a id="1"></a>

Jab Elasticsearch me hame documents ko kisi **custom logic ke basis par sort karna hota hai** (na ki alphabetically ya numerically), tab hum **script-based sorting** use karte hain.

---

## 2. 🧠 **Use Case Example:** <a id="2"></a>

Jese ki hamare paas ek field hai `positionType`:

```json
"positionType": "Software-Development"
```

Isme 4 values ho sakti hain:

```
Software-Development, Network-Engineering, IT-Support, Data-Management
```

Ham chahte hai ki **query ka results kuch is order me ho:**

```
Software-Development → Network-Engineering → IT-Support → Data-Management
```

Ab Elasticsearch normally `a → z` sort karta hai ya numeric value ke hisaab se. Par ye custom order **manually define** karna hai — isliye script sort lagayenge.

---

## 3. ⚙️ **Painless Script ka Syntax** <a id="3"></a>

Elasticsearch internally ek language use karta hai — **Painless**.

Script sort ka format hota hai:

```json
"sort": [
  {
    "_script": {
      "type": "number",
      "script": {
        "lang": "painless",
        "source": "tumhara_script_yahan"
      },
      "order": "asc"
    }
  }
]
```

---

## 4. 🧪 **Line-by-Line Breakdown** <a id="4"></a>

### 🔍 Sample Script:
```painless
if (doc['positionType.keyword'].value == 'Software-Development') return 0;
if (doc['positionType.keyword'].value == 'Network-Engineering') return 1;
if (doc['positionType.keyword'].value == 'IT-Support') return 2;
if (doc['positionType.keyword'].value == 'Data-Management') return 3;
return 4;
```

### 📖 Explanation:

| Line | Kya ho raha hai? |
|------|------------------|
| `doc['positionType.keyword'].value` | Ye `doc` ka matlab hai document ka ek field. `.keyword` isliye lagate hain taaki ye exact (non-analyzed) value de. |
| `if (value == 'Software-Development') return 0;` | Agar document ka positionType "Software-Development" hai, to uska sort order `0` ho jayega. |
| `return 0, 1, 2...` | Jo value ham return karte hai, Elasticsearch uske basis par sort karta hai. Lower value → higher rank. |
| `return 4;` | Agar koi aur value mil jaye jo expected nahi hai, to usse sabse end me daal do. |

---

## 5. ✅ **Final Real-world Example** <a id="5"></a>

```json
"sort": [
  {
    "_script": {
      "type": "number",
      "script": {
        "lang": "painless",
        "source": """
          if (doc['positionType.keyword'].value == 'Software-Development') return 0;
          if (doc['positionType.keyword'].value == 'Network-Engineering') return 1;
          if (doc['positionType.keyword'].value == 'IT-Support') return 2;
          if (doc['positionType.keyword'].value == 'Data-Management') return 3;
          return 4;
        """
      },
      "order": "asc"
    }
  }
]
```

👉 Ye bolega Elasticsearch ko:
- **Software-Development** pehle dikhana (0)
- **Network-Engineering** uske baad (1)
- **IT-Support** uske baad (2)
- **Data-Management** last me (3)
- Koi aur value? End me daal do (4)

---

## 6. 💡 Bonus Tips: <a id="6"></a>

- Always use `.keyword` if the field is of `text` type.
- Script sort thoda slow hota hai — sirf tab use karo jab zarurat ho.
- Error aaye to `doc['field']` me `.value` likhna na bhoolna.
- Script me ham arithmetic, if-else, even switch-case bhi use kar sakte hai.

---

## 🔚 Summary:

| Concept               | Easy Summary                      |
|-----------------------|-----------------------------------|
| Script Sort           | Custom logic se documents ko order karo |
| Painless Script       | Elasticsearch ki scripting language |
| `doc['field'].value`  | Document ki field access karne ka tarika |
| `return X`            | Sorting score define karta hai     |

---

