### **Range Searches in Elasticsearch**  

#### **📌 Table of Contents**  
1. 🔍 **Introduction to Range Searches**  
2. 🎯 **Basic Structure of a Range Query**  
3. 📊 **Searching Numeric Ranges**  
4. 📅 **Searching Date Ranges**  
5. 🕐 **Using Custom Date Formats**  
6. 🌍 **Handling Time Zones in Queries**  
7. 📝 **Summary**  

---  

### **🔍 Introduction to Range Searches**  
Ab tak humne dekha kaise **term aur terms queries** ka use karke exact values ko search kiya ja sakta hai. Lekin kai baar hume **range-based searches** karni hoti hain, jaise:  
✅ **Stock me kam bache products dikhana**  
✅ **Kisi specific date range ke beech me create hue products dikhana**  

Elasticsearch me **range queries** ka use karke aise searches perform ki ja sakti hain. Chalo ise detail me dekhte hain.  

---

### **🎯 Basic Structure of a Range Query**  
Elasticsearch me range query ka structure bilkul **baaki queries** jaisa hi hota hai.  
- Field ka naam specify karte hain  
- Uske andar **gte, lte, gt, lt** parameters set karte hain.  

Yaha `gte` aur `lte` ka matlab:  
- **gte (greater than or equal to)** → iska matlab **>=** hota hai  
- **lte (less than or equal to)** → iska matlab **<=** hota hai  

---

### **📊 Searching Numeric Ranges**  

Maan lo hume **1 se 5 stock quantity ke beech ke products** dikhane hain. To hum kuch aise query likhenge:  

#### **📌 Query**  
```json
{
  "query": {
    "range": {
      "in_stock": {
        "gte": 1,
        "lte": 5
      }
    }
  }
}
```  

Yeh SQL query ke barabar hoga:  
```sql
SELECT * FROM products WHERE in_stock BETWEEN 1 AND 5;
```  

#### **📌 Expected Output**
```json
{
  "hits": {
    "total": 3,
    "hits": [
      { "_id": "1", "_source": { "name": "Product A", "in_stock": 2 } },
      { "_id": "2", "_source": { "name": "Product B", "in_stock": 5 } },
      { "_id": "3", "_source": { "name": "Product C", "in_stock": 4 } }
    ]
  }
}
```  

👉 Agar **boundaries include nahi karni**, to `gte` aur `lte` ki jagah `gt` aur `lt` use karenge.  

```json
{
  "query": {
    "range": {
      "in_stock": {
        "gt": 1,
        "lt": 5
      }
    }
  }
}
```

Yeh query **1 aur 5 ko include nahi karegi.**  

---

### **📅 Searching Date Ranges**  
Agar hume **specific date range** me bane products dikhane hain, to hum date format me query likh sakte hain.  

#### **📌 Query**  
```json
{
  "query": {
    "range": {
      "created_at": {
        "gte": "2024-01-01",
        "lte": "2024-02-01"
      }
    }
  }
}
```

👉 **Default behavior:**  
- Agar time nahi diya to `00:00:00` se `23:59:59` tak ka data match hoga.  

#### **📌 Expected Output**
```json
{
  "hits": {
    "total": 2,
    "hits": [
      { "_id": "10", "_source": { "name": "Product X", "created_at": "2024-01-15" } },
      { "_id": "11", "_source": { "name": "Product Y", "created_at": "2024-01-25" } }
    ]
  }
}
```  

---

### **🕐 Using Custom Date Formats**  
Kuch scenarios me hum **different date formats** bhi use kar sakte hain.  

#### **📌 Query (Custom Date Format)**
```json
{
  "query": {
    "range": {
      "created_at": {
        "gte": "01-01-2024",
        "lte": "01-02-2024",
        "format": "dd-MM-yyyy"
      }
    }
  }
}
```

Yeh same **01 January 2024 - 01 February 2024** ke documents fetch karega.  

---

### **🌍 Handling Time Zones in Queries**  
Elasticsearch me **dates UTC (Coordinated Universal Time) format** me store hoti hain. Agar aap **local time zone** me query kar rahe ho, to time zone specify karna padega.  

#### **📌 Query (With Time Zone)**
```json
{
  "query": {
    "range": {
      "created_at": {
        "gte": "2024-01-01T00:00:00",
        "lte": "2024-02-01T23:59:59",
        "time_zone": "+05:30"
      }
    }
  }
}
```

👉 `+05:30` Indian Standard Time (IST) ko represent karta hai.  

---

### **📝 Summary**  
✅ **Range query** ka use hum **numeric aur date ranges** search karne ke liye karte hain.  
✅ `gte`, `lte`, `gt`, `lt` ka use boundaries set karne ke liye hota hai.  
✅ **Dates ke saath time zones** ka dhyan rakhna zaroori hota hai.  
✅ **Custom date formats** specify karke different formats me queries likh sakte hain.  

---

