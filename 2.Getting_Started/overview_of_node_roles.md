### 🚀 **Table of Contents**  

1️⃣ **[Introduction to Node Roles](#1)** 🏗️  
2️⃣ **[Master Node – Cluster ka boss](#2)** 🏆  
3️⃣ **[Data Node – Storage aur Query Execution](#3)** 🗄️  
4️⃣ **[Ingest Node – Data Preprocessing](#4)** 🔄  
5️⃣ **[Machine Learning Node – ML Jobs Execution](#5)** 🤖  
6️⃣ **[Coordinating Node – Query Distribution](#6)** 🔗  
7️⃣ **[Voting-Only Node – Master Election Support](#7)** 🗳️  
8️⃣ **[Default Node Roles in Elasticsearch](#8)** 🔍  
9️⃣ **[When to Modify Node Roles?](#9)** ⚙️  

---

## 1️⃣ **Introduction to Node Roles** 🏗️  <a id="1"></a>
Elasticsearch me **cluster ek ya zyada nodes ka collection hota hai**. Har node ka **koi ek ya zyada specific role hota hai**, jo define karta hai ki us node ka primary kaam kya hoga.  

**Shards kaha store hote hain?**  
- Data **shards me store hota hai**  
- Shards **nodes me store hote hain**  

By default, **har node shards store karta hai**, lekin hamesha aisa nahi hota. **Kuch nodes ka kaam sirf cluster ko manage karna hota hai, kuch ka sirf query distribute karna hota hai**, aur kuch ka sirf **machine learning jobs** run karna hota hai.  

Elasticsearch me **6 main node roles** hote hain:  

1. **Master Node** 🏆  
2. **Data Node** 🗄️  
3. **Ingest Node** 🔄  
4. **Machine Learning Node** 🤖  
5. **Coordinating Node** 🔗  
6. **Voting-Only Node** 🗳️  

Ab inko **detail me** dekhte hain.  

---

## 2️⃣ **Master Node 🏆 - Cluster ka Boss**  <a id="2"></a>
Master node **cluster-wide tasks handle karta hai**, jaise:  
✅ **New Index Create karna / Delete karna**  
✅ **Nodes ko track karna** (kaun online hai, kaun offline gaya)  
✅ **Shards ko allocate karna**  

### ❓ **Master Node Automatically Kaise Banta Hai?**  
- **Master node automatically elect hota hai** agar multiple nodes ke paas `master` role ho  
- Election **voting system** ke through hoti hai  
- **Stable master node hona bahut zaroori hai**, kyunki agar master node slow hoga, to **poora cluster unstable ho sakta hai**  

### **🔹 Real-Life Example**  
Maan lo ek **5 node cluster hai**, jisme 3 nodes master eligible hain.  
Agar ek master node pe **bahut zyada search queries execute ho rahi hain**, to uska CPU high ho jayega aur cluster **unstable ho sakta hai**.  
Solution: **Dedicated Master Nodes** rakhna.    

### ✅ **Best Practices**  
✔️ **Large clusters me alag se dedicated master nodes hone chahiye**  
✔️ Master node par **data store ya search execution nahi honi chahiye**  

---

## 3️⃣ **Data Node 🗄️ - Storage aur Query Execution**  <a id="3"></a>
Data nodes ka kaam hota hai:  
📌 **Data store karna**  
📌 **Search queries execute karna**  
📌 **Data modifications handle karna**  

### **🔹 Important Points**  
- **Chhote clusters me har node data node hota hai**  
- **Bade clusters me dedicated data nodes rakhna better hota hai**  

### **🔹 Example**  
Agar ek **large ecommerce website** hai, jisme daily **millions of search requests** aa rahi hain, to **dedicated data nodes rakhna best practice hota hai**, taaki master node overloaded na ho.  

### ✅ **Best Practices**  
✔️ **Data aur Master nodes ko separate karna**  
✔️ **High query load ke liye multiple data nodes rakhna**  

---

## 4️⃣ **Ingest Node 🔄 - Data Preprocessing**  <a id="4"></a>
Ingest nodes ka kaam hota hai **documents ko Elasticsearch me store karne se pehle modify karna**.  

### **🔹 Use Case - Web Server Logs Parsing**  
Agar tu **Apache access logs** Elasticsearch me store kar raha hai, to ingest node **IP addresses ko country me convert** kar sakta hai using a **GeoIP Processor**.  

### **🔹 Example Pipeline**  
```json
{
  "processors": [
    {
      "geoip": {
        "field": "client_ip",
        "target_field": "geo"
      }
    }
  ]
}
```
**📌 Isse hoga kya?**  
- `client_ip` se country, latitude, longitude extract ho jayega  
- Query results me ye additional fields dikhengi  

✅ **Best Practices**  
✔️ **Agar high volume data transform karna hai, to dedicated ingest node use karo**  
✔️ **Complex processing ke liye Logstash better hai**  

---

## 5️⃣ **Machine Learning Node 🤖 - ML Jobs Execution**  <a id="5"></a>
Machine Learning nodes **anomalies detect karne ke liye use hote hain**.  
Agar tu real-time **fraud detection ya log pattern analysis** kar raha hai, to ML nodes kaafi useful honge.  

### **🔹 Important Settings**  
- `"node.ml": true` → **Node ML jobs run kar sakta hai**  
- `"xpack.ml.enabled": true` → **ML API access enable hota hai**  

✅ **Best Practices**  
✔️ **Agar ML heavy tasks hai to dedicated ML nodes use karo**  
✔️ **ML jobs background me run hote hain, to other nodes slow na ho, iske liye alag node use karo**  

---

## 6️⃣ **Coordinating Node 🔗 - Query Distribution**  <a id="6"></a>
Coordinating nodes ka kaam hota hai:  
🔄 **Query ko sahi data nodes tak pahunchana**  
🔄 **Results collect karke response return karna**  

**⚠️ Important:**  
- **Har node coordinating node hota hai jab tak uske paas koi aur role assigned na ho**  
- **Bade clusters me dedicated coordinating nodes rakhe jate hain**  

✅ **Best Practices**  
✔️ **Agar bahut zyada queries aati hain to dedicated coordinating node rakho**  
✔️ **Isko ek tarah ka load balancer samjho jo queries ko sahi jagah route karta hai**  

---

## 7️⃣ **Voting-Only Node 🗳️ - Master Election Support**  <a id="7"></a>
Ye nodes sirf **voting me participate karte hain, but master nahi ban sakte**.  
**Large clusters me use hota hai** taaki **election stable ho aur unnecessary master changes na ho**.  

✅ **Best Practices**  
✔️ **Agar cluster unstable hai to extra voting nodes add karo**  
✔️ **Ye sirf bade clusters ke liye useful hai**  

---

## 8️⃣ **Default Node Roles in Elasticsearch** 🔍  <a id="8"></a>
Agar koi custom config nahi di jaye, to **har node ke paas ye roles hote hain:**  
📌 **Data (`d`)**  
📌 **Ingest (`i`)**  
📌 **Master (`m`)**  

**Example:**  
Kibana me agar `node.role` dekho, to `dim` ka matlab hoga **Data + Ingest + Master**.  

---

## 9️⃣ **When to Modify Node Roles? ⚙️**  <a id="9"></a>
Ye depend karta hai cluster size aur workload pe.  
📌 **Chhote clusters me default roles kaafi hain**  
📌 **Bade clusters me role separation better hai**  

✅ **Best Practices**  
✔️ **Agar hardware usage high hai, to roles ko split karo**  
✔️ **Cluster scale karne ke liye master aur data nodes separate karo**  

---

**🔥 Final Thought:**  
Elasticsearch me **sahi node roles assign karna** cluster ki **performance aur stability** ke liye bahut zaroori hai. Bade clusters me **dedicated master, data aur coordinating nodes** use karna best practice hai.  

