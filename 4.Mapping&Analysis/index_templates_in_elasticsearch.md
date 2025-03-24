# **Index Templates in Elasticsearch**  
Index templates Elasticsearch me ek **powerful feature** hai jo naye banne wale indices par automatically **settings aur field mappings** apply karne ka kaam karta hai. Ye templates **pattern-based rules** ka use karke naye indices par apply ho jate hain.

## **📜 Table of Contents**  
1️⃣ [Index Templates Kya Hain?](#1)  
2️⃣ [Index Templates Ka Structure](#2)  
3️⃣ [Index Templates Ki Zaroorat Kyu Hoti Hai?](#3)  
4️⃣ [Index Templates Kaise Kaam Karte Hain?](#4)  
5️⃣ [Index Templates Ka Example](#5)  
6️⃣ [Index Templates Kaise Create Karein?](#6)  
7️⃣ [Index Templates Kaise Update Aur Delete Karein?](#7)  
8️⃣ [Index Template Priorities](#8)  
9️⃣ [Kab Index Templates Use Karna Chahiye?](#9)  
🔟 [Conclusion](#10)  

---

## **1️⃣ Index Templates Kya Hain?**  <a id="1"></a>
**Index templates** Elasticsearch me ek aise mechanism hain jo **naye indices par automatically settings aur mappings apply** kar dete hain **jab bhi unka naam kisi pattern se match hota hai**.  
Yani agar tum **dynamic indexing** kar rahe ho (jaise time-based indices create kar rahe ho), toh har baar manually settings aur mappings apply karne ki zaroorat nahi hoti.

⏩ **Example Use Case:**  
Agar tumhara system **monthly logs** store karta hai jaise:
- `logs-2025-01`
- `logs-2025-02`
- `logs-2025-03`  
Toh tum ek **index template** create kar sakte ho jo har naye `logs-*` index par automatically settings aur mappings apply kar de.

---

## **2️⃣ Index Templates Ka Structure**  <a id="2"></a>
Ek **index template** JSON format me likha jata hai aur usme **3 main parts** hote hain:

1. **`index_patterns`** → Yeh batata hai ki template kaunse index names par apply hoga (wildcards `*` use kar sakte hain).
2. **`template`** → Iske andar `settings` aur `mappings` define kiye jate hain.
3. **`priority`** → Agar multiple templates kisi ek index ke liye match ho rahe hain, toh **priority** decide karti hai kaunsa apply hoga.

📌 **Example:**
```json
PUT _index_template/logs_template
{
  "index_patterns": ["logs-*"],
  "priority": 10,
  "template": {
    "settings": {
      "number_of_shards": 2
    },
    "mappings": {
      "properties": {
        "timestamp": { "type": "date" },
        "message": { "type": "text" },
        "status_code": { "type": "integer" }
      }
    }
  }
}
```
✅ Yeh template **saare `logs-*` indices** par apply hoga aur automatically:
- **2 shards** set karega,
- **timestamp ko `date` type**,  
- **message ko `text` type**,  
- **status_code ko `integer` type** set karega.

---

## **3️⃣ Index Templates Ki Zaroorat Kyu Hoti Hai?**  <a id="3"></a>
Index templates tab useful hote hain jab:
- **Har mahine ya har din naye indices create ho rahe ho** (jaise logs ya analytics data).
- **Manually har naye index ke liye mappings aur settings apply na karni pade**.
- **Default Elasticsearch settings kaafi na ho** aur custom settings chahiye ho.
- **Consistency maintain karni ho** across multiple indices.

---

## **4️⃣ Index Templates Kaise Kaam Karte Hain?**  <a id="4"></a>
Elasticsearch me jab bhi ek **naya index create hota hai**, toh yeh steps follow hote hain:

1. **Check karta hai ki index exist karta hai ya nahi**  
   - Agar index pehle se exist karta hai, toh bas data store hota hai.
2. **Agar index exist nahi karta toh Elasticsearch matching index templates dhundta hai**.
3. **Sabse high priority wale template ka configuration apply hota hai**.
4. **Index create hota hai with the applied template settings**.
5. **Data index me store ho jata hai**.

Yeh **automatic process** hai, hume index manually create karne ki zaroorat nahi hoti.

---

## **5️⃣ Index Templates Ka Example**  <a id="5"></a>
### **📝 Without Index Template:**
Agar tum bina index template ke ek document store karte ho:
```json
POST logs-2025-03/_doc/1
{
  "timestamp": "2025-03-01T12:00:00",
  "message": "User logged in",
  "status_code": 200
}
```
Toh Elasticsearch automatically ek **default index** create karega jo:
- **1 shard aur 1 replica** rakhega.
- **Field types guess karega**, jo **galat bhi ho sakta hai**.

### **✅ With Index Template:**
Agar pehle se ek index template apply hai, toh yeh data automatically sahi settings ke sath store hoga.

---

## **6️⃣ Index Templates Kaise Create Karein?**  <a id="6"></a>
Agar tum **Kibana Console ya API** se index template create karna chahte ho, toh **PUT request** ka use karna hoga.

```json
PUT _index_template/my_template
{
  "index_patterns": ["my_index-*"],
  "priority": 5,
  "template": {
    "settings": { "number_of_shards": 1 },
    "mappings": {
      "properties": {
        "title": { "type": "text" },
        "views": { "type": "integer" }
      }
    }
  }
}
```
🔹 Yeh template **saare `my_index-*` wale indices** par apply hoga.

---

## **7️⃣ Index Templates Kaise Update Aur Delete Karein?**  <a id="7"></a>
**🔄 Update:**  
Agar tum ek index template ko **update** karna chahte ho, toh same **PUT request** bhejo.

```json
PUT _index_template/my_template
{
  "index_patterns": ["my_index-*"],
  "priority": 10,
  "template": {
    "settings": { "number_of_shards": 2 },
    "mappings": {
      "properties": {
        "title": { "type": "text" },
        "views": { "type": "integer" },
        "likes": { "type": "integer" }
      }
    }
  }
}
```
✅ **New field `likes` add ho gaya aur priority badh gayi.**

**🗑 Delete:**  
Agar template delete karna hai, toh **DELETE request** use karo:
```json
DELETE _index_template/my_template
```

---

## **8️⃣ Index Template Priorities**  <a id="8"></a>
Agar multiple index templates ek hi index pattern ke liye match ho rahe hain, toh **priority decide karti hai kaunsa apply hoga**.

**Example:**  
```json
PUT _index_template/general_template
{
  "index_patterns": ["logs-*"],
  "priority": 5,
  "template": { "settings": { "number_of_shards": 3 } }
}

PUT _index_template/specific_template
{
  "index_patterns": ["logs-2025-*"],
  "priority": 10,
  "template": { "settings": { "number_of_shards": 1 } }
}
```
⚡ **Result:** `logs-2025-*` wale indices `specific_template` follow karenge kyunki uski **priority 10 hai** jo **general_template (priority 5) se zyada hai**.

---

## **9️⃣ Kab Index Templates Use Karna Chahiye?**  <a id="9"></a>
✅ **Jab multiple similar indices create ho rahe ho**  
✅ **Jab har naye index ke liye same settings aur mappings apply karni ho**  
✅ **Jab manually index create karna avoid karna ho**  

❌ **Agar sirf ek hi index banana hai toh index template ki zaroorat nahi hoti.**

---

## **🔟 Conclusion**  <a id="10"></a>
Index templates **automation aur consistency** maintain karne ke liye **best practice** hai. Ye naye indices ke liye:
- **Settings & Mappings automatically apply kar dete hain**  
- **Manual configuration ki zaroorat khatam kar dete hain**  
- **Sharding aur scaling easy bana dete hain**  

🚀 **Agar tum Elasticsearch me time-based ya dynamic indices use kar rahe ho, toh Index Templates must-have feature hai!**

---
[Reference Video Link](https://youtu.be/cAAZNbWZpgg?si=Byvev2BoYlwzxgoS)
---