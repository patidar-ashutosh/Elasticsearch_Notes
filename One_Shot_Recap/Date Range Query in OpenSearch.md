# **📌 Date Range Query in OpenSearch**  

## **📌 Introduction**  
`date_range` query ka use **date fields** pe filtering karne ke liye hota hai.  
- Agar tumhe **specific time period** ke andar ke documents chahiye, to `date_range` query use hoti hai.  
- Yeh **gte, lte, gt, lt** operators ke saath kaam karti hai, jisme tum start aur end date specify kar sakte ho.  
- OpenSearch **ISO 8601 date format (`YYYY-MM-DD`)** ya **epoch time (milliseconds)** ko support karta hai.  

📌 **Use Cases:**  
- **Last 7 days ke documents dikhana**  
- **Specific year/month ke data ko filter karna**  
- **Events ya logs ke time-based queries chalana**  

---

## **📌 Syntax (Short & Simple)**  
```json
GET index_name/_search
{
  "query": {
    "range": {
      "date_field": {
        "gte": "start_date",
        "lte": "end_date"
      }
    }
  }
}
```
🔹 **index_name** → Jis index me search karna hai.  
🔹 **date_field** → Jis date field pe range apply karni hai.  
🔹 **gte (greater than or equal)** → Minimum date specify karta hai.  
🔹 **lte (less than or equal)** → Maximum date specify karta hai.  
🔹 **gt / lt** bhi use kar sakte ho:  
   - `gt` → Greater than (`>`), yani us date ke baad ka data.  
   - `lt` → Less than (`<`), yani us date se pehle ka data.  

---

## **✅ Example**  
Hum ek index **events** banayenge jisme date-based filtering karenge.

### **Step 1: Index & Data Creation**
```json
PUT events
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "event_date": { "type": "date" }
    }
  }
}
```

```json
POST events/_bulk
{ "index": { "_id": 1 } }
{ "name": "New Year Party", "event_date": "2024-01-01" }
{ "index": { "_id": 2 } }
{ "name": "Tech Conference", "event_date": "2024-02-15" }
{ "index": { "_id": 3 } }
{ "name": "Music Festival", "event_date": "2023-12-20" }
{ "index": { "_id": 4 } }
{ "name": "Sports Tournament", "event_date": "2023-11-10" }
{ "index": { "_id": 5 } }
{ "name": "Diwali Celebration", "event_date": "2023-10-24" }
```

---

## **✅ Example 1: Last 3 Months ke Events**  
Agar tumhe sirf **2023-11-01 ke baad** wale events dikhane hain:  
```json
GET events/_search
{
  "query": {
    "range": {
      "event_date": {
        "gte": "2023-11-01"
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
      { "_id": "1", "_source": { "name": "New Year Party", "event_date": "2024-01-01" } },
      { "_id": "2", "_source": { "name": "Tech Conference", "event_date": "2024-02-15" } },
      { "_id": "3", "_source": { "name": "Music Festival", "event_date": "2023-12-20" } }
    ]
  }
}
```
✔ **Sirf 2023-11-01 ke baad ke events aaye.**  

---

## **✅ Example 2: Specific Year ke Events (`2023`)**  
Agar tumhe sirf **2023 wale events** chahiye:  
```json
GET events/_search
{
  "query": {
    "range": {
      "event_date": {
        "gte": "2023-01-01",
        "lte": "2023-12-31"
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
      { "_id": "3", "_source": { "name": "Music Festival", "event_date": "2023-12-20" } },
      { "_id": "4", "_source": { "name": "Sports Tournament", "event_date": "2023-11-10" } },
      { "_id": "5", "_source": { "name": "Diwali Celebration", "event_date": "2023-10-24" } }
    ]
  }
}
```
✔ **Sirf 2023 wale events return hue.**  

---

## **✅ Example 3: Last 30 Days ke Events (Relative Date)**  
Agar tumhe **last 30 days ke events chahiye**, to **now** keyword ka use kar sakte ho:  
```json
GET events/_search
{
  "query": {
    "range": {
      "event_date": {
        "gte": "now-30d/d",
        "lte": "now"
      }
    }
  }
}
```
✔ **`now-30d/d` ka matlab hota hai exact 30 din pehle se lekar aaj tak ke events.**  

---

## **✅ Example 4: Future Events (`event_date > today`)**  
Agar tumhe sirf **aaj ke baad wale events** chahiye:  
```json
GET events/_search
{
  "query": {
    "range": {
      "event_date": {
        "gt": "now"
      }
    }
  }
}
```
✔ **Sirf future events return honge.**  

---

## **🔹 Epoch Time Use Karna (Milliseconds Since 1970)**  
Agar tum **epoch time (UNIX timestamp in milliseconds)** use kar rahe ho, to bhi range query kaam karegi:  
```json
GET events/_search
{
  "query": {
    "range": {
      "event_date": {
        "gte": 1672531200000,
        "lte": 1704067200000
      }
    }
  }
}
```
✔ **Ye query sirf un events ko fetch karegi jo `2023-01-01` se `2024-01-01` ke beech me hain.**  

---

## **📌 Key Takeaways**  
✔ **Date-based filtering ke liye `range` query ka use hota hai.**  
✔ **`gte`, `lte`, `gt`, `lt` operators ka use hota hai.**  
✔ **Relative dates (`now`, `now-7d/d`) ka use last X days ke liye hota hai.**  
✔ **Epoch time (milliseconds) bhi supported hai.**  
✔ **Performance improve karne ke liye date fields ko `date` type me index karna zaroori hai.**  

