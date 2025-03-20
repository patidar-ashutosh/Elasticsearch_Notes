# 📌 Specifying the Result Format

## 📖 Table of Contents  

1️⃣ **🚀 Introduction: Why Result Formatting Matters?**  
2️⃣ **📝 Converting JSON to YAML Output**  
3️⃣ **🎨 Pretty JSON Formatting for Readability**  
4️⃣ **💻 Running Queries in Terminal (cURL Example)**  
5️⃣ **⚠️ When to Use Pretty Formatting?**  
6️⃣ **🔚 Summary & Key Takeaways**  

---

## 1️⃣ 🚀 Introduction: Why Result Formatting Matters?  

Elasticsearch **by default JSON format me results return karta hai**, lekin kabhi-kabhi **readability improve karne ke liye formatting zaroori hoti hai**.  

✔️ **Kya seekhne wale hain?**  
✅ **JSON ko YAML me convert kaise karein?**  
✅ **JSON results ko human-readable banane ka tareeka**  
✅ **Terminal (cURL) me query results kaise dekhein?**  

---

## 2️⃣ 📝 Converting JSON to YAML Output  

👉 **Elasticsearch ke response ko YAML format me convert karne ke liye ek simple query parameter add karna hota hai.**  

### 🔹 **Example:**  
```bash
GET my_index/_search?format=yaml
```

🛠️ **YAML me response ka example:**  
```yaml
hits:
  total: 1
  max_score: 1.0
  hits:
    - _index: "my_index"
      _id: "1"
      _source:
        name: "John Doe"
        age: 30
```
✔️ **YAML readable hota hai aur indentation maintain karta hai.**  
✔️ **JSON ke alternative ke roop me useful hota hai.**  

---

## 3️⃣ 🎨 Pretty JSON Formatting for Readability  

By default, Elasticsearch ke raw JSON results **compact hote hain**, jo ki terminal me **hard to read** hote hain.  

### 🔹 **Example (Without Formatting):**
```json
{"took":10,"timed_out":false,"hits":{"total":1,"hits":[{"_id":"1","_source":{"name":"John","age":30}}]}}
```
😵 **Itna compact format human-readable nahi hota!**  

**Solution:** `pretty` query parameter add karo.  

### 🔹 **Example (With Pretty Formatting)**  
```bash
GET my_index/_search?pretty
```

🛠️ **Formatted Output:**
```json
{
  "took": 10,
  "timed_out": false,
  "hits": {
    "total": 1,
    "hits": [
      {
        "_id": "1",
        "_source": {
          "name": "John",
          "age": 30
        }
      }
    ]
  }
}
```
✔️ **Much better readability!**  

---

## 4️⃣ 💻 Running Queries in Terminal (cURL Example)  

Agar tum Kibana console ki jagah **directly terminal me queries run kar rahe ho**, to format manually set karna padega.  

### 🔹 **Basic cURL Request (Without Formatting)**
```bash
curl -X GET "http://localhost:9200/my_index/_search"
```
👀 **Raw JSON Output:**  
```json
{"took":10,"timed_out":false,"hits":{"total":1,"hits":[{"_id":"1","_source":{"name":"John","age":30}}]}}
```
😵 **Difficult to read!**  

### 🔹 **Formatted cURL Request**
```bash
curl -X GET "http://localhost:9200/my_index/_search?pretty"
```
🎉 **Readable Output!**  

✔️ **Better for debugging!**  

---

## 5️⃣ ⚠️ When to Use Pretty Formatting?  

🛠️ **Use pretty formatting:**  
✅ **Debugging ke waqt**  
✅ **Terminal me human-readable response ke liye**  
✅ **Logs ko easily analyze karne ke liye**  

🚨 **Avoid pretty formatting:**  
❌ **Production environment me**  
❌ **Automated scripts me (Jisse API response size increase na ho)**  

---

## 6️⃣ 🔚 Summary & Key Takeaways  

✅ **JSON ko YAML format me convert kar sakte hain (`?format=yaml`)**  
✅ **Pretty JSON response ke liye (`?pretty`) parameter use karein**  
✅ **cURL aur Terminal queries me readability improve hoti hai**  
✅ **Debugging ke waqt `pretty` useful hai, par production me avoid karein**  

---

