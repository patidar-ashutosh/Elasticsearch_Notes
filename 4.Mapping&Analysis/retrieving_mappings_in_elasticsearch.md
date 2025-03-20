# 📜 Retrieving Mappings in Elasticsearch  

## 📑 **Table of Contents**  
1. 🔍 **Mapping API Overview**  
2. 📥 **Retrieve Full Mapping of an Index**  
3. 🎯 **Retrieve Mapping of a Specific Field**  
4. 🏗️ **Understanding Dot Notation for Nested Objects**  
5. 🛠️ **Real-World Example with Expected Output**  
6. 📌 **Key Insights Beyond the Video**  

---  

## 🔍 **1. Mapping API Overview**  
Elasticsearch me mapping ek tarika hai jisme bataya jata hai ki documents ke fields kis type ke hone chahiye. Kabhi kabhi hume manually mapping define karni padti hai, lekin agar **dynamic mapping** enabled hai, to Elasticsearch apne aap field types detect kar leta hai.  

Lekin, **dynamic mapping** unpredictable ho sakta hai, isliye hume kabhi kabhi check karna padta hai ki Elasticsearch ne fields ko kis type me map kiya hai. Isi kaam ke liye **Mapping API** use hoti hai.  

---  

## 📥 **2. Retrieve Full Mapping of an Index**  
Agar kisi index ka pura mapping structure dekhna ho, to hum **GET request** bhej sakte hain:  

### 🔹 **API Request:**  
```sh
GET reviews/_mapping
```
Ya agar sabhi indexes ka mapping ek saath dekhna ho to:  
```sh
GET _all/_mapping
```

### 🟢 **Expected Output:**  
```json
{
  "reviews": {
    "mappings": {
      "properties": {
        "title": { "type": "text" },
        "rating": { "type": "integer" },
        "author": {
          "properties": {
            "name": { "type": "text" },
            "email": { "type": "keyword" }
          }
        }
      }
    }
  }
}
```
Yeh output batata hai ki **reviews** index me **title** field text hai, **rating** integer hai, aur **author** ke andar nested object hai jisme **name** aur **email** fields hain.  

---  

## 🎯 **3. Retrieve Mapping of a Specific Field**  
Agar kisi ek specific field ka mapping dekhna ho, to **field parameter** use kar sakte hain:  

### 🔹 **API Request:**  
```sh
GET reviews/_mapping/field/rating
```

### 🟢 **Expected Output:**  
```json
{
  "reviews": {
    "mappings": {
      "rating": { "type": "integer" }
    }
  }
}
```
Yeh confirm karta hai ki **rating** field integer type ki hai.  

---  

## 🏗️ **4. Understanding Dot Notation for Nested Objects**  
Agar koi field ek **nested object** ke andar ho, to uska mapping retrieve karne ke liye **dot notation** ka use hota hai.  

For example, agar **author** ke andar **email** field ka mapping check karna ho:  

### 🔹 **API Request:**  
```sh
GET reviews/_mapping/field/author.email
```

### 🟢 **Expected Output:**  
```json
{
  "reviews": {
    "mappings": {
      "author.email": { "type": "keyword" }
    }
  }
}
```
Isse pata chalta hai ki **email** field ka type **keyword** hai.  

---  

## 🛠️ **5. Real-World Example with Expected Output**  
Man lo tumhari team Elasticsearch me **dynamic mapping** enable karke documents index kar rahi hai. Tum chahte ho verify karna ki Elasticsearch ne correct types detect kiye hain ya nahi.  

### **Step 1: Insert Document Without Predefined Mapping**  
```sh
POST reviews/_doc/1
{
  "title": "Great Book",
  "rating": 5,
  "author": {
    "name": "John Doe",
    "email": "john@example.com"
  }
}
```
Yeh document insert hone ke baad Elasticsearch **automatically mapping** create karega.  

### **Step 2: Retrieve Mapping**  
```sh
GET reviews/_mapping
```

### **Expected Output:**  
```json
{
  "reviews": {
    "mappings": {
      "properties": {
        "title": { "type": "text" },
        "rating": { "type": "long" },
        "author": {
          "properties": {
            "name": { "type": "text" },
            "email": { "type": "keyword" }
          }
        }
      }
    }
  }
}
```
**Key Observation:**  
- **rating** ko Elasticsearch ne **long** me convert kar diya (jo integer ka extended version hai).  
- **email** ko **keyword** me store kiya gaya hai (jo exact matching ke liye useful hota hai).  

Iska matlab hai ki agar tum chahte ho **integer** hi use ho, to manually mapping define karni hogi.  

---  

## 📌 **6. Key Insights Beyond the Video**  
✅ **Mapping Retrieve Karne Ke Alag-Alag Tarike**  
- Poora mapping dekhne ke liye: `GET index_name/_mapping`  
- Specific field ka mapping dekhne ke liye: `GET index_name/_mapping/field/field_name`  
- Sabhi indexes ka mapping dekhne ke liye: `GET _all/_mapping`  

✅ **Mapping Retrieve Karne Ka Use Case**  
- Agar **dynamic mapping enabled** ho aur tum verify karna chaho ki Elasticsearch ne fields ko kis type me map kiya hai.  
- Query likhne se pehle mapping check karna useful hota hai taki aggregation ya filters properly kaam karein.  
- Agar kisi **nested object** me deep mapping dekhni ho, to **dot notation** use karni padti hai.  

✅ **Mapping Ka Difference: Dynamic vs. Manual**  
- **Dynamic mapping** me Elasticsearch apni marzi se types assign karta hai.  
- **Manual mapping** me tum pehle se bata sakte ho ki kaunsa field kis type ka hoga, jo zyada control deta hai.  

---  

### 🎯 **Conclusion**  
Ab tumhe **Mapping API** use karne ka pura process samajh aa gaya hoga. Tum kisi bhi index ka mapping retrieve kar sakte ho, specific fields ka mapping dekh sakte ho, aur nested objects ke liye **dot notation** use kar sakte ho.  

