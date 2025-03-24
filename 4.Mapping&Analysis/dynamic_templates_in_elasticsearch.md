## **Dynamic Templates in Elasticsearch**  

### **Table of Contents**  

1️⃣ [Introduction](#1)  
2️⃣ [What are Dynamic Templates?](#2)  
3️⃣ [Structure of Dynamic Templates](#3)  
4️⃣ [How Dynamic Templates Work](#4)  
5️⃣ [Defining Match Conditions](#5)  
   - 🔹 [match_mapping_type](#5-1)  
   - 🔹 [match and unmatch](#5-2)  
   - 🔹 [match_pattern with Regex](#5-3)  
   - 🔹 [path_match and path_unmatch](#5-4)  

6️⃣ [Using Placeholders in Dynamic Templates](#6)  
7️⃣ [Example Queries](#7)  
8️⃣ [Difference Between Dynamic Templates and Index Templates](#8)  
9️⃣ [Conclusion](#9)  

---

## **1. Introduction**  <a id="1"></a>
Elasticsearch me data ko store karne ke liye ek **mapping** hoti hai, jisme bataya jata hai ki kaunsa field kis type ka hoga (string, integer, date, etc.). **Dynamic Mapping** automatically naye fields ko detect kar leta hai aur default data type assign kar deta hai. Lekin kabhi-kabhi hume chahiye hota hai ki naye fields ek specific rule follow karein, iske liye **Dynamic Templates** ka use hota hai.

---

## **2. What are Dynamic Templates?**  <a id="2"></a>
Dynamic Templates ek **powerful feature** hai jo naye fields ke **mapping rules** ko define karne me madad karta hai. Jab **dynamic mapping enable** hoti hai aur koi **naya field milta hai**, tab **dynamic templates check** kiya jata hai ki kya koi predefined rule available hai.  

### **Key Features:**  
✅ Kisi bhi naye field ke liye custom mapping apply karna  
✅ Multiple conditions ke basis pe fields ka mapping decide karna  
✅ Default dynamic mapping behavior ko override karna  

---

## **3. Structure of Dynamic Templates**  <a id="3"></a>
Dynamic Templates **mappings** ke andar define kiye jate hain aur `"dynamic_templates"` key ke andar likhe jate hain.  

```json
{
  "mappings": {
    "dynamic_templates": [
      {
        "template_name": {
          "match_mapping_type": "long",
          "mapping": {
            "type": "integer"
          }
        }
      }
    ]
  }
}
```
Yahaan `"long"` type ko `"integer"` me convert kar diya gaya hai.

---

## **4. How Dynamic Templates Work**  <a id="4"></a>
Jab **Elasticsearch** ek naye field ko detect karta hai, tab ye check karta hai:  
1. Kya **existing mapping** me ye field defined hai?  
2. Agar nahi, to **kya koi dynamic template available hai jo match hota hai?**  
3. Agar **koi dynamic template match hota hai**, to uss mapping rule ko apply kiya jata hai.  
4. Agar koi **dynamic template match nahi hota**, to Elasticsearch **default dynamic mapping** ka use karta hai.  

---

## **5. Defining Match Conditions**  <a id="5"></a>
Dynamic Templates me fields ko match karne ke liye multiple conditions hoti hain.

### **5.1 match_mapping_type**  <a id="5-1"></a>
Yeh parameter kisi bhi **JSON data type** ke basis pe matching karta hai.

```json
{
  "dynamic_templates": [
    {
      "integers_template": {
        "match_mapping_type": "long",
        "mapping": {
          "type": "integer"
        }
      }
    }
  ]
}
```
**Explanation:**  
✅ Agar JSON me koi **long** type ka number milega, to use **integer** me convert kar diya jayega.  
✅ Default behavior change ho jayega, kyunki Elasticsearch normal case me **long** ko long hi rakhta hai.  

### **5.2 match and unmatch**  <a id="5-2"></a>
Field ke **name pattern** ke basis pe matching karne ke liye **match** aur **unmatch** parameters use hote hain.

```json
{
  "dynamic_templates": [
    {
      "text_template": {
        "match": "text_*",
        "unmatch": "*_keyword",
        "mapping": {
          "type": "text"
        }
      }
    },
    {
      "keyword_template": {
        "match": "*_keyword",
        "mapping": {
          "type": "keyword"
        }
      }
    }
  ]
}
```
**Explanation:**  
✅ `"text_*"` wale fields automatically **text** type me map ho jayenge.  
✅ Lekin agar field `"*_keyword"` se end hota hai, to wo **keyword** me convert ho jayega.  

### **5.3 match_pattern with Regex**  <a id="5-3"></a>
Agar **wildcards** se kaam nahi chal raha, to **Regular Expressions (Regex)** ka use kar sakte hain.

```json
{
  "dynamic_templates": [
    {
      "regex_template": {
        "match_pattern": "regex",
        "match": ".*_name",
        "mapping": {
          "type": "text"
        }
      }
    }
  ]
}
```
**Explanation:**  
✅ Jo fields **"_name"** se end honge, unko **text** me convert kar diya jayega.  
✅ Regex se **complex patterns** match kiye ja sakte hain.

### **5.4 path_match and path_unmatch**  <a id="5-4"></a>
Yeh parameters **nested objects** ke full path pe kaam karte hain.

```json
{
  "dynamic_templates": [
    {
      "full_name_template": {
        "path_match": "name.*",
        "mapping": {
          "type": "text",
          "copy_to": "full_name"
        }
      }
    }
  ]
}
```
**Explanation:**  
✅ `"name.*"` ke andar jitne bhi fields honge (`name.first_name`, `name.last_name`), unko **text** type me map kiya jayega.  
✅ `"copy_to": "full_name"` ka matlab hai ki sabhi values **full_name** field me copy ho jayengi.  

---

## **6. Using Placeholders in Dynamic Templates**  <a id="6"></a>
Kahi baar hume **dynamic_type** placeholders ka use karna padta hai.

```json
{
  "dynamic_templates": [
    {
      "default_template": {
        "match_mapping_type": "*",
        "mapping": {
          "type": "{dynamic_type}",
          "index": false
        }
      }
    }
  ]
}
```
**Explanation:**  
✅ Jo bhi **naya field milega**, uska data type wahi rahega jo Elasticsearch detect karega.  
✅ `"index": false` hone ka matlab hai ki is field pe **searching disable** rahegi.  
✅ Ye **time series data** ke liye useful hai.  

---

## **7. Example Queries**  <a id="7"></a>
#### **Create Index with Dynamic Template**
```json
PUT my_index
{
  "mappings": {
    "dynamic_templates": [
      {
        "numbers_template": {
          "match_mapping_type": "long",
          "mapping": {
            "type": "integer"
          }
        }
      }
    ]
  }
}
```
#### **Index a Document**
```json
PUT my_index/_doc/1
{
  "age": 25
}
```
#### **Check Mapping**
```json
GET my_index/_mapping
```
**Expected Output:**  
✅ `"age"` field automatically `"integer"` me convert ho jayega.  

---

## **8. Difference Between Dynamic Templates and Index Templates**  <a id="8"></a>
| Feature | Dynamic Templates | Index Templates |
|---------|------------------|----------------|
| Applied On | **Fields** | **Indexes** |
| Purpose | **New fields ki mapping rules set karta hai** | **Index creation time pe predefined settings set karta hai** |
| Scope | **Field-level** rules | **Index-level** rules |
| Flexibility | **Dynamic matching** based on conditions | **Static rules** |

---

## **9. Conclusion**  <a id="9"></a>
✅ **Dynamic Templates** ka use karke hum **naye fields** ke mapping rules define kar sakte hain.  
✅ Different **match conditions** ka use karke specific data types aur field names ke liye **custom mappings** apply kiya ja sakta hai.  
✅ Ye **default Elasticsearch mapping** se zyada **flexible** aur **customizable** approach provide karta hai.