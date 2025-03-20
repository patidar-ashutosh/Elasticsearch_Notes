# **🗂 Table of Contents**
1️⃣ [Introduction](#introduction)
2️⃣ [Understanding `delete_by_query`](#understanding-delete_by_query)
3️⃣ [Basic Example](#basic-example)
4️⃣ [Handling Version Conflicts](#handling-version-conflicts)
5️⃣ [Advanced Use Cases](#advanced-use-cases)
6️⃣ [Comparison with `update_by_query`](#comparison-with-update_by_query)
7️⃣ [Conclusion](#conclusion)

---

# **🛑 Delete By Query in Elasticsearch**

## **1️⃣ Introduction**

Elasticsearch me **`delete_by_query`** ka use **multiple documents** ko ek sath delete karne ke liye hota hai jo kisi particular **condition ko match** karte hain.

> 💡 **Matlab:** Tum **kisi condition ke basis pe ek sath multiple documents ko delete** kar sakte ho bina manually ek-ek document ko delete kiye.

## **2️⃣ Understanding `delete_by_query`**

`delete_by_query` API **search query** ke basis pe documents delete karta hai. Yeh **query match hone wale sabhi documents** ko delete karega. Yeh bulk operations me useful hota hai jab tumhe **expired data cleanup** ya **outdated records delete** karne ho.

## **3️⃣ Basic Example**

Agar tumhe **jitne bhi products `"in_stock": 0"` hain unko delete karna ho**, to tum `delete_by_query` ka use kar sakte ho:

```http
POST /products/_delete_by_query
{
  "query": {
    "term": {
      "in_stock": 0
    }
  }
}
```

✅ **Yeh Elasticsearch me se sabhi `"in_stock": 0"` wale products delete kar dega.**

## **4️⃣ Handling Version Conflicts**

Agar **query execute karte waqt koi version conflict aata hai**, to tum **`conflicts: proceed`** parameter ka use kar sakte ho taki query abort na ho aur jo documents delete ho sakte hain, wo delete ho jayein:

```http
POST /products/_delete_by_query
{
  "query": {
    "term": {
      "in_stock": 0
    }
  },
  "conflicts": "proceed"
}
```

✅ **Yeh query version conflicts ko ignore karke delete operation continue karegi.**

## **5️⃣ Advanced Use Cases**

### **🟢 Example: Delete Orders Before 2023**

Agar tumhe **jitne bhi orders `"order_date": "2022-12-31"` ya usse pehle ke hain unko delete karna ho**, to:

```http
POST /orders/_delete_by_query
{
  "query": {
    "range": {
      "order_date": {
        "lte": "2022-12-31"
      }
    }
  }
}
```

✅ **Isse sabhi 2022 ya usse pehle ke orders delete ho jayenge.**

### **🟢 Example: Delete All Documents in an Index**

Agar tumhe **poora index delete nahi karna, sirf uske andar ke sabhi documents delete karne hain**, to:

```http
POST /products/_delete_by_query
{
  "query": {
    "match_all": {}
  }
}
```

✅ **Yeh `products` index ke sabhi documents delete karega, lekin index itself delete nahi hoga.**

## **6️⃣ Comparison with `update_by_query`**

| Feature  | `update_by_query` | `delete_by_query` |
|----------|-----------------|-----------------|
| **Function** | Multiple documents ko update karta hai | Multiple documents ko delete karta hai |
| **Uses** | Fields modify karne ke liye | Data cleanup ke liye |
| **Performance** | Thoda slow ho sakta hai kyunki update me scripting lagta hai | Fast hota hai kyunki sirf delete operation perform hota hai |
| **Use Cases** | Price increase, field add/remove, bulk data correction | Expired data delete karna, purani entries remove karna |

## **7️⃣ Conclusion**

✅ **`delete_by_query` ka use multiple documents ko delete karne ke liye hota hai** jo kisi condition ko match karte hain.  
✅ **Yeh bulk deletion ke liye efficient aur fast tarika hai.**  
✅ **Agar tumhe version conflicts handle karne hain, to `conflicts: proceed` parameter ka use kar sakte ho.**

💡 **Use this API wisely, kyunki yeh irreversible changes perform karta hai!** 🚀

---
[Reference Video Link](https://youtu.be/m9nQM6QH4J4?si=K02zOI1ocx0lT2-T)
---

