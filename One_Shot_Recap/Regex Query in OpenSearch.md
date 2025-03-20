# **📌 Regex Query in OpenSearch**  

## **📌 Introduction**  
OpenSearch me **regex query** ka use **pattern-based search** ke liye hota hai.  
- Agar hume **wildcards ya patterns ke basis pe documents filter karne hain**, to regex query ka use kar sakte hain.  
- **Regular Expressions (`regex`) powerful search techniques hain jo complex text matching karne ke liye use hote hain.**  

📌 **Use Cases:**  
✔ **Strings me patterns match karna** (Jaise `"opensearch"` ke saath `"open.*"` match hoga).  
✔ **Dynamic search implement karna** (Jaise kisi bhi naam ka prefix check karna).  
✔ **Emails ya IDs ka format check karna**.  

---

## **📌 Syntax (Short & Simple)**
```json
GET index_name/_search
{
  "query": {
    "regexp": {
      "field_name": "regex_pattern"
    }
  }
}
```
🔹 **index_name** → Jis index me search karna hai.  
🔹 **field_name** → Jis field pe regex apply karna hai.  
🔹 **regex_pattern** → Jo pattern match karna hai.  

---

## **✅ Example 1: Simple Regex Query**  
Agar hum **`name` field** me **"open" se start hone wale sabhi words** find karna chahte hain:
```json
GET my_index/_search
{
  "query": {
    "regexp": {
      "name": "open.*"
    }
  }
}
```
✔ **Yeh `"opensearch"`, `"opentech"`, `"openai"` jaise values match karega.**  

---

## **✅ Example 2: Regex for Numeric Pattern**  
Agar hume **exact 5-digit IDs** dhoondhni hain:
```json
GET my_index/_search
{
  "query": {
    "regexp": {
      "id": "[0-9]{5}"
    }
  }
}
```
✔ **Yeh sirf aise IDs match karega jo 5 digits ki hain (Jaise `12345`, `98765`).**  

---

## **✅ Example 3: Email Format Validation**
Agar hume **email fields me valid emails** check karni hain:
```json
GET my_index/_search
{
  "query": {
    "regexp": {
      "email": ".*@gmail\\.com"
    }
  }
}
```
✔ **Sirf `gmail.com` wale emails match honge (Jaise `test@gmail.com`).**  

---

## **📌 Important Points**  
✔ **Regex query slow ho sakti hai agar large dataset ho.**  
✔ **Full-text search ke liye `match` query better hoti hai.**  
✔ **Keyword fields (`.keyword`) pe regex fast work karega.**  
✔ **Wildcards (`.*`, `[0-9]`, `[a-z]`) ka use pattern-based search ke liye hota hai.**  

