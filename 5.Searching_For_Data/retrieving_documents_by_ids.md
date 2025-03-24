# 📌 Retrieving Documents by IDs  

## 📖 Table of Contents  

1️⃣ [🔍 Introduction](#1)  
2️⃣ [🆔 IDs Query in Elasticsearch](#2)  
3️⃣ [🛠 Query Structure](#3)  
4️⃣ [⚡ Running the Query & Expected Output](#4)  
5️⃣ [🏁 Conclusion](#5)  

---

## 1. 🔍 **Introduction**  <a id="1"></a>
Pehle humne dekha tha ki ek single document kaise retrieve karte hain uske unique **_id** ke basis pe.  
Ab hum dekhenge ki ek **term-level query** ka use karke ek saath multiple documents retrieve kaise karein.  

---

## 2. 🆔 **IDs Query in Elasticsearch**  <a id="2"></a>
Elasticsearch me ek **"ids" query** hoti hai jo specifically **document IDs** ke basis pe search karti hai.  
Agar tumhe ek se zyada specific documents chahiye, to bas unke **IDs ka array** pass kar do aur kaam ho gaya!  

---

## 3. 🛠 **Query Structure**  <a id="3"></a>
Yeh raha ek example query jo teen specific documents retrieve karegi:

```json
GET my_index/_search
{
  "query": {
    "ids": {
      "values": ["1", "2", "3"]
    }
  }
}
```
💡 **Explanation:**  
- **"ids"**: Query ka type batata hai ki hum IDs ke basis pe search kar rahe hain.  
- **"values"**: Yeh ek array hai jo un documents ke IDs contain karega jo humein chahiye.  

---

## 4. ⚡ **Running the Query & Expected Output**  <a id="4"></a>
Jab yeh query run karenge, to **3 documents return honge** agar woh index me available hain.  
Agar koi ID nahi mili to Elasticsearch us ID ko ignore karega, lekin jo documents match hue woh return ho jayenge.

**Expected Output:**
```json
{
  "hits": {
    "total": 3,
    "hits": [
      {
        "_id": "1",
        "_source": { "name": "Apple", "category": "Fruit" }
      },
      {
        "_id": "2",
        "_source": { "name": "Carrot", "category": "Vegetable" }
      },
      {
        "_id": "3",
        "_source": { "name": "Milk", "category": "Dairy" }
      }
    ]
  }
}
```
👉 **Note:** Agar kisi bhi ID ka document nahi milta, to sirf existing documents return honge.  

---

## 5. 🏁 **Conclusion**  <a id="5"></a>
✅ IDs query Elasticsearch me fast aur efficient hoti hai jab humein **specific documents retrieve** karne hote hain.  
✅ Agar tumhara database relational hai aur IDs maintain kar raha hai, to yeh approach **best practice** hai.  
✅ Agar tumne 10 IDs diye hain to **0 se 10 documents tak return ho sakte hain**, bas itna yaad rakhna!  

---

