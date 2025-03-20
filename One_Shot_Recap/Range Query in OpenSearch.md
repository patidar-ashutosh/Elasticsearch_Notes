# **Range Query in OpenSearch**  

## **📌 Introduction**  
**Range Query** ka use **numeric, date, or keyword fields** pe **greater than (>`>`), less than (`<`), between (`TO`)** jaise conditions apply karne ke liye hota hai.  
- Agar tumhe **price, date, age** ya kisi aur numeric field ke basis pe filtering karni hai, to **range query** ka use hota hai.  
- **Comparison operators** ke through **minimum aur maximum values define** ki jati hain.  

📌 **Use Cases:**  
- **Price filter** lagana (`price > 1000`)  
- **Date range search** karna (`date between 2023-01-01 and 2023-12-31`)  
- **Numeric fields ko limit karna** (`age < 30`)  

---

## **📌 Syntax (Short & Simple)**  
```json
GET index_name/_search
{
  "query": {
    "range": {
      "field_name": {
        "gte": value1,
        "lte": value2
      }
    }
  }
}
```
🔹 **index_name** → Jis index me search karna hai.  
🔹 **field_name** → Jis field pe range apply karni hai.  
🔹 **gte** → Greater than or equal (`>=`).  
🔹 **lte** → Less than or equal (`<=`).  
🔹 **gt / lt** bhi use kar sakte ho:  
   - `gt`: Greater than (`>`)  
   - `lt`: Less than (`<`)  

---

## **✅ Example**  
Hum ek index **products** banayenge aur uspe **range query** ka use karenge.

### **Step 1: Index & Data Creation**
```json
PUT products
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "price": { "type": "integer" },
      "release_date": { "type": "date" }
    }
  }
}
```

```json
POST products/_bulk
{ "index": { "_id": 1 } }
{ "name": "iPhone 13", "price": 799, "release_date": "2023-09-14" }
{ "index": { "_id": 2 } }
{ "name": "Samsung Galaxy S21", "price": 699, "release_date": "2021-01-29" }
{ "index": { "_id": 3 } }
{ "name": "OnePlus 9", "price": 729, "release_date": "2021-03-23" }
{ "index": { "_id": 4 } }
{ "name": "Google Pixel 6", "price": 599, "release_date": "2021-10-19" }
{ "index": { "_id": 5 } }
{ "name": "Sony Xperia 5", "price": 899, "release_date": "2022-09-01" }
```

---

## **✅ Example 1: Price Greater Than 700 (`price > 700`)**
```json
GET products/_search
{
  "query": {
    "range": {
      "price": {
        "gt": 700
      }
    }
  }
}
```
### **🟢 Expected Output**
```json
{
  "hits": {
    "hits": [
      { "_id": "1", "_source": { "name": "iPhone 13", "price": 799 } },
      { "_id": "3", "_source": { "name": "OnePlus 9", "price": 729 } },
      { "_id": "5", "_source": { "name": "Sony Xperia 5", "price": 899 } }
    ]
  }
}
```
✔ **Sirf woh products aaye jinka price `700` se zyada hai.**  

---

## **✅ Example 2: Price Between 600 and 800 (`600 <= price <= 800`)**
```json
GET products/_search
{
  "query": {
    "range": {
      "price": {
        "gte": 600,
        "lte": 800
      }
    }
  }
}
```
### **🟢 Expected Output**
```json
{
  "hits": {
    "hits": [
      { "_id": "1", "_source": { "name": "iPhone 13", "price": 799 } },
      { "_id": "2", "_source": { "name": "Samsung Galaxy S21", "price": 699 } },
      { "_id": "3", "_source": { "name": "OnePlus 9", "price": 729 } },
      { "_id": "4", "_source": { "name": "Google Pixel 6", "price": 599 } }
    ]
  }
}
```
✔ **Sirf woh products aaye jinka price `600` se `800` ke beech me hai.**  

---

## **✅ Example 3: Filter by Date Range (`release_date >= 2022-01-01`)**
```json
GET products/_search
{
  "query": {
    "range": {
      "release_date": {
        "gte": "2022-01-01"
      }
    }
  }
}
```
### **🟢 Expected Output**
```json
{
  "hits": {
    "hits": [
      { "_id": "1", "_source": { "name": "iPhone 13", "release_date": "2023-09-14" } },
      { "_id": "5", "_source": { "name": "Sony Xperia 5", "release_date": "2022-09-01" } }
    ]
  }
}
```
✔ **Sirf woh products aaye jo `2022-01-01` ke baad release hue hain.**  

---

## **📌 Key Takeaways**  
✔ **Range Query ka use numeric, date, aur keyword fields pe filtering ke liye hota hai.**  
✔ **Operators: `gt` (greater than), `gte` (greater than or equal), `lt` (less than), `lte` (less than or equal).**  
✔ **Performance optimize karne ke liye range queries indexed fields pe lagani chahiye.**  
✔ **Date filtering ke liye ISO format `"YYYY-MM-DD"` use hota hai.**  

