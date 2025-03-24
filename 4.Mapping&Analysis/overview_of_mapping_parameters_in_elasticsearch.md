# Overview of Mapping Parameters in Elasticsearch

## Table of Contents  
1️⃣ [Introduction](#1)  
2️⃣ [Format Parameter](#2)  
3️⃣ [Properties Parameter](#3)  
4️⃣ [Coerce Parameter](#4)  
5️⃣ [Doc Values Parameter](#5)  
6️⃣ [Norms Parameter](#6)  
7️⃣ [Index Parameter](#7)  
8️⃣ [Null Value Parameter](#8)  
9️⃣ [Copy To Parameter](#9)  
🔟 [Conclusion](#10)  

---

## 1. Introduction <a id="1"></a>
Elasticsearch me jab hum mapping define karte hain to hume sirf fields aur unke data types specify nahi karne hote, balki unka behavior bhi control kar sakte hain. Ye behavior control karne ke liye mapping parameters ka use hota hai. Is document me hum kuch important mapping parameters ka detailed explanation dekhenge.

---

## 2. Format Parameter <a id="2"></a>
### **Kya hai?**
- Jab hum date-type fields ko store karte hain, tab default format hota hai **ISO 8601**.
- Agar kisi reason se tumhe custom format use karna pade, to `format` parameter ka use kiya jata hai.
- Ye **Java’s DateFormatter class** ke compatible formats accept karta hai.

### **Example**
```json
{
  "mappings": {
    "properties": {
      "created_at": {
        "type": "date",
        "format": "yyyy-MM-dd HH:mm:ss"
      }
    }
  }
}
```
Is example me date format `yyyy-MM-dd HH:mm:ss` rakha gaya hai jo ki `2025-02-28 14:30:00` jaisa dikhega.

---

## 3. Properties Parameter <a id="3"></a>
### **Kya hai?**
- `properties` parameter use hota hai **nested fields** define karne ke liye.
- Elasticsearch me "object" data type nahi hota, balki hum **properties parameter** ka use karke object type structure define karte hain.

### **Example**
```json
{
  "mappings": {
    "properties": {
      "user": {
        "properties": {
          "name": { "type": "text" },
          "age": { "type": "integer" }
        }
      }
    }
  }
}
```
Yaha `user` ek nested object hai jisme `name` aur `age` fields hain.

---

## 4. Coerce Parameter <a id="4"></a>
### **Kya hai?**
- Jab hum document insert karte hain aur kisi field ka type alag hota hai, to Elasticsearch **automatically convert (coerce)** karne ki koshish karta hai.
- Is behavior ko control karne ke liye `coerce` parameter use hota hai.
- Default: `true` (Enable hota hai)

### **Example**
```json
{
  "mappings": {
    "properties": {
      "price": {
        "type": "integer",
        "coerce": false
      }
    }
  }
}
```
Agar hum ab "price" field me string `"100"` bhejenge to Elasticsearch **error throw karega** kyunki `coerce` disabled hai.

---

## 5. Doc Values Parameter <a id="5"></a>
### **Kya hai?**
- Elasticsearch indexing ke liye **Apache Lucene** ka use karta hai, aur waha ek special data structure hota hai **doc_values**.
- Ye mainly **sorting, aggregations aur scripting** ke liye use hota hai.
- Agar tumhe pata hai ki kisi field ka use **sirf search ke liye hoga aur sorting/aggregation nahi chahiye**, to tum `doc_values: false` kar sakte ho taaki disk space bache.

### **Example**
```json
{
  "mappings": {
    "properties": {
      "message": {
        "type": "text",
        "doc_values": false
      }
    }
  }
}
```
---

## 6. Norms Parameter <a id="6"></a>
### **Kya hai?**
- Elasticsearch **relevance score** calculate karne ke liye "norms" store karta hai.
- Agar tumhe kisi field pe full-text search aur ranking nahi karni, to norms ko disable karke **disk space bacha sakte ho**.

### **Example**
```json
{
  "mappings": {
    "properties": {
      "tags": {
        "type": "text",
        "norms": false
      }
    }
  }
}
```

---

## 7. Index Parameter <a id="7"></a>
### **Kya hai?**
- Agar tum kisi field ko **searchable nahi banana chahte**, lekin uska data "_source" object me store karna chahte ho, to `index: false` use kar sakte ho.

### **Example**
```json
{
  "mappings": {
    "properties": {
      "metadata": {
        "type": "keyword",
        "index": false
      }
    }
  }
}
```
Ab `metadata` field index nahi hogi, lekin `_source` me available rahegi.

---

## 8. Null Value Parameter <a id="8"></a>
### **Kya hai?**
- Elasticsearch by default **NULL values ko ignore** karta hai.
- Agar tum NULL values ko searchable banana chahte ho, to `null_value` parameter use kar sakte ho.

### **Example**
```json
{
  "mappings": {
    "properties": {
      "status": {
        "type": "keyword",
        "null_value": "UNKNOWN"
      }
    }
  }
}
```
Agar `status` field NULL hogi to Elasticsearch automatically "UNKNOWN" ko index karega.

---

## 9. Copy To Parameter <a id="9"></a>
### **Kya hai?**
- Agar tum ek field ki values **ek nayi field me copy karna chahte ho** to `copy_to` parameter use hota hai.

### **Example**
```json
{
  "mappings": {
    "properties": {
      "first_name": {
        "type": "text",
        "copy_to": "full_name"
      },
      "last_name": {
        "type": "text",
        "copy_to": "full_name"
      },
      "full_name": {
        "type": "text"
      }
    }
  }
}
```
Is example me `first_name` aur `last_name` ki values automatically `full_name` field me copy ho jayengi.

---

## 10. Conclusion <a id="10"></a>
Ye the kuch **important mapping parameters** jo Elasticsearch me fields ke behavior ko control karte hain. Har parameter ka apna specific use case hai, aur tumhe mapping design karte waqt carefully plan karna hoga ki kin parameters ka use karna hai.

Agar tum Elasticsearch me high-performance indexing aur querying chahte ho, to ye parameters samajhna bahut zaroori hai!

