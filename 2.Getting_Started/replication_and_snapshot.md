## **📌 Table of Contents**  

1️⃣ **[Replication Kya Hai?](#1)**  
2️⃣ **[Replication Ka Working (With Example)](#2)**  
3️⃣ **[Replication Configuration in Elasticsearch](#3)**  
   - **[Dynamic Replication Set Karna](#3-1)**  
   - **[Manually Replicas Set Karna](#3-2)**  
   - **[Best Practice](#3-3)**  

4️⃣ **[🎯 Conclusion & Summary Of Replication](#4)**  

5️⃣ **[Snapshots in Elasticsearch](#5)**  
6️⃣ **[📖 Snapshots Kya Hote Hain?](#6)**  
7️⃣ **[Snapshot Ka Working (With Example)](#7)**  
   - **[Snapshot Repository Banayein](#7-1)**  
   - **[Snapshot Create Karna](#7-2)**  
   - **[Snapshot Restore Karna](#7-3)**  

8️⃣ **[🎯 Summary Of Snapshot](#8)**  
9️⃣ **[Snapshot vs Replication](#9)**  
   - **[🆚 Snapshot vs Replication: Automatic Update Hota Hai Ya Nahi?](#9-1)**  
   - **[🆚 Snapshot vs Replica: Best Option for Data Recovery?](#9-2)**  
   - **[🆚 Snapshot vs Replica: Kaun Best Hoga?](#9-3)**  

🔟 **[🎯 Conclusion: Snapshot Best Hai!](#10)**  

---

## 1️⃣ **📌 Replication Kya Hai?**  <a id="1"></a>
**Replication** ka matlab hai **data ka ek ya zyada copies (backup) banana**, taaki **agar ek node fail ho jaye, to bhi data accessible rahe**.  

Elasticsearch **har shard ka ek ya zyada "replica shards" create karta hai**, jo **primary shard ka exact copy hote hain**.  

✅ **Replication ka main purpose:**  
1️⃣ **High Availability** – Agar ek node crash ho jaye, to data lost na ho.  
2️⃣ **Load Balancing** – Multiple copies hone se search queries tez chalti hain.  
3️⃣ **Fault Tolerance** – Data corruption ya hardware failure se protection milta hai.  

---

## 2️⃣ **📌 Replication Ka Working (With Example)**  <a id="2"></a>
Socho ek **index "products"** hai, jo **2 primary shards** me divide hai:  
```plaintext
Index: products
Primary Shards:  
  - Shard 1  
  - Shard 2  
```
Agar tum **1 replica configure karte ho**, to **har shard ka ek replica shard ban jayega**:  

```plaintext
Index: products
Primary Shards:  
  - Shard 1 (Node A)  
  - Shard 2 (Node B)  

Replica Shards:  
  - Replica of Shard 1 (Node B)  
  - Replica of Shard 2 (Node A)  
```
🚀 **Ab agar Node A fail ho jaye, to Node B me "Shard 1 ka replica" present hoga, aur system smoothly kaam karega!**  

---

## 3️⃣ **📌 Replication Configuration in Elasticsearch**  <a id="3"></a>
Jab tum index create karte ho, to **replicas ka number manually set kar sakte ho**:  

```bash
PUT /products
{
  "settings": {
    "number_of_shards": 2,    # 2 Primary Shards
    "number_of_replicas": 1   # 1 Replica per shard
  }
}
```
✔️ **Matlab:** Har **primary shard ka ek replica banega**, jo kisi doosre node pe store hoga.  

---

**Ham Number of replica ko dynamically bhi set kar sakte hai** aur Elasticsearch ko automatically adjust karne de sakte hai **jitne nodes cluster me honge uske hisaab se replicas adjust karega**.  


## **📌 1️⃣ Dynamic Replication Set Karna**  <a id="3-1"></a>
Agar tum **replica count dynamically adjust karna chahte ho**, to tum **"auto_expand_replicas"** setting use kar sakte ho.  

```bash
PUT /products/_settings
{
  "index": {
    "auto_expand_replicas": "0-3"
  }
}
```
✅ **Iska Matlab:**  
- **Agar sirf 1 node hai → Replica 0 hoga (kyunki koi dusra node available nahi hai).**  
- **Agar 2 nodes hai → Replica 1 hoga.**  
- **Agar 4 nodes hai → Replica 3 tak expand ho sakta hai.**  

📌 **Elasticsearch automatically cluster ke nodes ke hisaab se replication adjust karega.**  

---

## **📌 2️⃣ Manually Replicas Set Karna**  <a id="3-2"></a>
Agar tum manually **replica count change karna chahte ho**, to ye command run kar sakte ho:  

```bash
PUT /products/_settings
{
  "index": {
    "number_of_replicas": 2
  }
}
```
✅ **Isse "products" index ke har shard ka 2 replica create ho jayega.**  

---

## **📌 Best Practice**  <a id="3-3"></a>
✔ **Agar cluster size dynamic hai (nodes aate-jate rehte hain), to "auto_expand_replicas" use karna best hai.**  
✔ **Agar cluster fixed hai, to manually number_of_replicas set kar sakte ho.**  

---

## 4️⃣ **🎯 Conclusion**  <a id="4"></a>
✅ **Haan, tum dynamically replica set kar sakte ho "auto_expand_replicas" use karke.**  
✅ **Elasticsearch automatically cluster ke nodes ke hisaab se replica adjust kar dega.**  
✅ **Agar manually control chahiye, to tum "number_of_replicas" update kar sakte ho.**  

---

## **🎯 Summary Of Replication:**
✅ **Replication Elasticsearch me data backup aur high availability ke liye hoti hai.**  
✅ **Replica shards primary shards ka exact copy hote hain, jo queries ko tez banate hain.**  
✅ **Agar ek node fail ho jaye, to replica shard active ho jata hai, aur data loss nahi hota.**  
✅ **Elasticsearch automatically replicas ko alag nodes pe distribute karta hai.**  



## 5️⃣ **📌 Snapshots in Elasticsearch**  <a id="5"></a>

### 6️⃣ **📖 Snapshots Kya Hote Hain?**  <a id="6"></a>
**Snapshot** ek **backup** hota hai jo **Elasticsearch ke indices ya pura cluster ka data save karne ke liye** use hota hai. Agar kabhi **data delete ho jaye ya system fail ho jaye, to snapshot se data restore kiya ja sakta hai**.  

✅ **Snapshots mainly long-term backup aur disaster recovery ke liye use hote hain.**  
✅ **Ye data ko cloud storage (S3, GCS, Azure), file system, ya remote storage pe save kar sakte hain.**  

---

## 7️⃣ **📌 Snapshot Ka Working (With Example)**  <a id="7"></a>
### **1️⃣ Snapshot Repository Banayein**  <a id="7-1"></a>
Snapshot store karne ke liye pehle ek **repository** banana padta hai. Example:  

```bash
PUT _snapshot/my_backup_repo
{
  "type": "fs",
  "settings": {
    "location": "/mnt/backup"   # Yahan backup store hoga
  }
}
```
🔹 **"fs" type ka matlab hai ki backup local file system pe store hoga.**  

---

### **2️⃣ Snapshot Create Karna**  <a id="7-2"></a>
Agar tum **"products" index ka snapshot lena chahte ho**, to ye command use karo:  

```bash
PUT _snapshot/my_backup_repo/snapshot_1
{
  "indices": "products",    # Jiska backup lena hai
  "ignore_unavailable": true,
  "include_global_state": false
}
```
✔️ **Isse "products" index ka snapshot "snapshot_1" naam se store ho jayega.**  

---

### **3️⃣ Snapshot Restore Karna**  <a id="7-3"></a>
Agar tum **snapshot se data wapas lana chahte ho**, to ye command run karo:  

```bash
POST _snapshot/my_backup_repo/snapshot_1/_restore
{
  "indices": "products"
}
```
✔️ **Isse "products" index ka data restore ho jayega.**  

---

## 8️⃣ **🎯 Summary Of Snapshot**  <a id="8"></a>
✅ **Snapshot Elasticsearch me data ka long-term backup hota hai.**  
✅ **Isse data ko future me restore kiya ja sakta hai agar kuch delete ya corrupt ho jaye.**  
✅ **Snapshots ko local file system ya cloud storage me store kiya ja sakta hai.**  
✅ **Ye replication se alag hai kyunki replication sirf node failure se protect karta hai, jabki snapshots pura system crash hone par bhi data recover kar sakte hain.**  

---

## 9️⃣ **📌 Snapshot vs Replication**  <a id="9"></a>
| Feature | Snapshot | Replication |
|---------|----------|------------|
| **Purpose** | Long-term backup | High availability |
| **Storage** | External storage (disk, cloud) | Elasticsearch nodes |
| **Failure Protection** | System crash se bachata hai | Node failure se bachata hai |
| **Data Recovery** | Manually restore karna padta hai | Automatically ho jata hai |

---

### **📌 Snapshot vs Replication: Automatic Update Hota Hai Ya Nahi?**  <a id="9-1"></a>

| Feature | **Replication** | **Snapshot** |
|---------|---------------|-------------|
| **Automatic Update?** | ✅ **Haan, Automatically Update Hota Hai** | ❌ **Nahi, Manually Trigger Karna Padta Hai** |
| **Real-time Data Sync?** | ✅ **Haan, Real-time me Primary Shard se Replica Shard sync hota hai** | ❌ **Nahi, Snapshot lene ke time ka data save hota hai** |
| **Failure Protection** | **Node Failure se bachata hai** | **Data Deletion/System Crash se bachata hai** |
| **Storage** | **Elasticsearch ke internal nodes me** | **External storage (Disk, Cloud, Remote FS)** |

---

### **📌 Snapshot vs Replica: Best Option for Data Recovery?**  <a id="9-2"></a>

#### **🔥 Scenario:**  
1️⃣ Tumhare **"products"** index me **"apple"** ka data hai.  
2️⃣ Tumne **Snapshot liya** (Backup bana liya).  
3️⃣ Data **Replication me bhi store hai** (Primary + Replica Shard dono me).  
4️⃣ **Apple ka data galti se update ho gaya.**  
5️⃣ Tumhe **previous data wapas chahiye**.  

---

### **🆚 Snapshot vs Replica: Kaun Best Hoga?** <a id="9-3"></a>

| Feature | **Replication** | **Snapshot** |
|---------|---------------|-------------|
| **Automatically Update?** | ✅ **Haan, Real-time Update hota hai** | ❌ **Nahi, Manual Trigger karna padta hai** |
| **Data Overwrite Hoga?** | ✅ **Haan, Latest Update Replica me bhi reflect hoga** | ❌ **Nahi, Snapshot me Old Data safe rahega** |
| **Old Data Recover Kar Sakte Ho?** | ❌ **Nahi, Kyunki Replica me bhi Latest Data aa gaya hai** | ✅ **Haan, Tum Snapshot se Previous Data Restore Kar Sakte Ho** |
| **Best For This Case?** | ❌ **Nahi, Kyunki Update ho chuka hai** | ✅ **Haan, Best Option hai!** |

---

### 🔟 **🎯 Conclusion: Snapshot Best Hai!**  <a id="10"></a>
✅ **Agar galti se koi data update ho gaya hai aur tumhe old data wapas chahiye, to Snapshot best option hai.**  
❌ **Replica kaam nahi karega, kyunki wo real-time me primary shard se sync hota hai. Agar primary shard me update ho gaya, to replica me bhi update ho jayega.**  
📌 **Isliye Regular Snapshots lena important hota hai, taaki galti se kuch update hone par tum easily restore kar sako.**  