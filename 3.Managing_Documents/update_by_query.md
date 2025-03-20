# **🔹 Update By Query in Elasticsearch**

## 📌 **Table of Contents**
1. [Introduction](#introduction)
2. [How `update_by_query` Works](#how-update_by_query-works)
3. [Basic Syntax](#basic-syntax)
4. [Examples](#examples)
   - [Updating a Field Value](#updating-a-field-value)
   - [Adding a New Field](#adding-a-new-field)
   - [Removing a Field](#removing-a-field)
5. [Snapshot Mechanism](#snapshot-mechanism)
6. [Handling Version Conflicts](#handling-version-conflicts)
7. [Performance Considerations](#performance-considerations)
8. [Comparison with `delete_by_query`](#comparison-with-delete_by_query)
9. [Conclusion](#conclusion)

---

## 🔹 **Introduction**
Elasticsearch me **`update_by_query`** ka use **multiple documents** ko ek sath update karne ke liye hota hai. Yeh ek powerful feature hai jo relational databases ke `UPDATE ... WHERE` clause jaisa kaam karta hai.

> 💡 **Matlab:** Tum **kisi condition ke basis pe ek sath multiple documents ko update** kar sakte ho bina manually ek-ek document ko update kiye.

---

## 🔹 **How `update_by_query` Works**
1. **Query Execution:** Pehle, Elasticsearch **search query** execute karta hai taaki woh un documents ko dhund sake jo update hone chahiye.
2. **Snapshot Creation:** Query execute hone se pehle ek **snapshot** liya jata hai taaki documents update ke waqt consistent rahein.
3. **Bulk Updates:** Jo documents search se match hote hain, unpe ek **bulk update request** bheji jati hai.
4. **Version Conflicts:** Agar kisi document ka version change ho gaya hai update ke waqt, toh **version conflict** aa sakta hai.

---

## 🔹 **Basic Syntax**

```http
POST /index_name/_update_by_query
{
  "script": {
    "source": "ctx._source.field_name = 'new_value'"
  },
  "query": {
    "match": {
      "field_name": "some_value"
    }
  }
}
```
✔ **Yeh query sirf unhi documents ko update karegi jo `field_name` ka value `some_value` hai.**

---

## 🔹 **Examples**

### **🔹 Updating a Field Value**
Agar tumhe **sabhi `electronics` category ke products ka price 10% se badhana ho**, toh:

```http
POST /products/_update_by_query
{
  "script": {
    "source": "ctx._source.price *= 1.10"
  },
  "query": {
    "match": {
      "category": "electronics"
    }
  }
}
```
✔ **Price 10% increase ho jayega sirf `electronics` category ke products me.**

---

### **🔹 Adding a New Field**
Agar tum **sabhi `kitchen` category ke products me ek naya field `discount: true` add karna chahte ho**, toh:

```http
POST /products/_update_by_query
{
  "script": {
    "source": "ctx._source.discount = true"
  },
  "query": {
    "match": {
      "category": "kitchen"
    }
  }
}
```
✔ **`discount: true` field add ho jayega jitne bhi `category: kitchen` wale products hain.**

---

### **🔹 Removing a Field**
Agar tum **`out_of_stock` field ko hata dena chahte ho sabhi documents se**, toh:

```http
POST /products/_update_by_query
{
  "script": {
    "source": "ctx._source.remove('out_of_stock')"
  },
  "query": {
    "exists": {
      "field": "out_of_stock"
    }
  }
}
```
✔ **Sabhi documents me se `out_of_stock` field remove ho jayega.**

---

## 🔹 **Snapshot Mechanism**
Elasticsearch **snapshot create karta hai** taaki update operation ke dauraan documents ka original state preserve ho sake.

> 🛑 **Matlab:** Agar update ke waqt koi document modify ho raha ho kisi aur request se, toh snapshot ensure karta hai ki correct version update ho.

---

## 🔹 **Handling Version Conflicts**
Agar kisi document ka **version change ho gaya** update process ke dauraan, toh **version conflict** aa sakta hai aur query fail ho sakti hai.

✅ **Conflict ko ignore karne ke liye tum `conflicts: proceed` ka use kar sakte ho:**

```http
POST /products/_update_by_query?conflicts=proceed
{
  "script": {
    "source": "ctx._source.price *= 1.10"
  },
  "query": {
    "match": {
      "category": "electronics"
    }
  }
}
```
✔ **Isse conflict wale documents skip ho jayenge, aur baki sab update ho jayenge.**

---

## 🔹 **Performance Considerations**
✔ **`update_by_query` bulk me documents update karta hai, isliye yeh performance pe impact dal sakta hai.**
✔ **Agar bahut saare documents update ho rahe hain, toh yeh cluster load badha sakta hai.**
✔ **Best Practice:** Use **small batch sizes** aur **index refresh rate adjust karo** taaki performance optimize ho sake.

---

## 🔹 **Comparison with `delete_by_query`**
| Feature  | `update_by_query` | `delete_by_query` |
|----------|-----------------|-----------------|
| **Function** | Multiple documents ko update karta hai | Multiple documents ko delete karta hai |
| **Uses** | Fields modify karne ke liye | Data cleanup ke liye |
| **Performance** | Thoda slow ho sakta hai kyunki update me scripting lagta hai | Fast hota hai kyunki sirf delete operation perform hota hai |
| **Use Cases** | Price increase, field add/remove, bulk data correction | Expired data delete karna, purani entries remove karna |

---

## 🔹 **Conclusion**
✅ **`update_by_query` ka use multiple documents ko update karne ke liye hota hai** jo kisi condition ko match karte hain.  
✅ **Snapshot mechanism ensure karta hai ki updates correct data ke basis pe ho.**  
✅ **Version conflicts ko `conflicts: proceed` se handle kiya ja sakta hai.**  
✅ **Yeh Elasticsearch ka ek powerful feature hai jo bulk updates efficiently perform karta hai.** 🚀

---

[Reference Video Link](https://youtu.be/m9nQM6QH4J4?si=K02zOI1ocx0lT2-T) 🎥

