## **📌 Table of Contents**  

1️⃣ **[Elasticsearch Architecture Overview 🚀](#1)**  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 [Nodes & Clusters](#1-1)  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 [Data Distribution & Scalability](#1-2)  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 [Documents & Indices](#1-3)  

2️⃣ **[Inspecting the Cluster 🧐](#2)**  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 [Kibana Console ka Use](#2-1)  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 [Cluster Health Check 🏥](#2-2)  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 [Nodes List Check 🖥️](#2-3)  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 [Indices List Check 📂](#2-4)  

3️⃣ **[Conclusion 🎯](#3)**  

---

## 1️⃣ **Elasticsearch Architecture Overview 🚀**  <a id="1"></a>

### **1️⃣ Nodes & Clusters**  <a id="1-1"></a>
- Jab tum Elasticsearch start karte ho, toh ek **node** start hota hai.  
- **Node = Elasticsearch ka ek instance** jo data store karta hai.  
- Multiple nodes milkar **cluster** banate hain.  
- Cluster = Nodes ka collection jo saara data store aur manage karta hai.  
- Tum ek machine pe multiple nodes bhi chala sakte ho, but production mein har node alag machine/container pe hona chahiye.  

### **2️⃣ Data Distribution & Scalability**  <a id="1-2"></a>
- Elasticsearch **distributed system** hai, iska matlab data ko multiple nodes pe distribute kiya jata hai.  
- Agar tumhare paas **terabytes of data** hai, toh multiple nodes bana sakte ho jo alag-alag machines pe run karenge.  
- Har node data ka ek **hissa** store karega, jisse scalability aur reliability improve hoti hai.  

### **3️⃣ Documents & Indices**  <a id="1-3"></a>
- Data **documents** ke form mein store hota hai (JSON objects).  
- Documents **indices** ke andar store hote hain.  
- **Index = Similar documents ka logical group** (e.g., "people" index mein sabhi logon ke documents honge).  
- Indices scalability aur availability ko optimize karne ke liye configure kiye ja sakte hain.  

---

## 2️⃣ **Inspecting the Cluster 🧐**  <a id="2"></a>

### **1️⃣ Kibana Console ka Use**  <a id="2-1"></a>
- **Dev Tools → Console** open karo.  
- Elasticsearch REST API ko queries bhejne ke liye Console tool ka use hota hai.  
- HTTP methods (GET, POST, PUT, DELETE) se requests bhejte hain.  

### **2️⃣ Cluster Health Check 🏥**  <a id="2-2"></a>
```http
GET /_cluster/health
```
- Ye request cluster ka **health status** check karti hai:  
  - **Green** 🟢 → Sab kuch theek hai.  
  - **Yellow** 🟡 → Data available hai, but redundancy missing hai.  
  - **Red** 🔴 → Kuch shards unavailable hain, cluster unhealthy hai.  

### **3️⃣ Nodes List Check 🖥️**  <a id="2-3"></a>
```http
GET /_cat/nodes?v
```
- Ye query **cluster ke sabhi nodes** ki info dikhati hai, including:  
  - Node name  
  - IP address  
  - Roles (Master, Data, Ingest, etc.)  

### **4️⃣ Indices List Check 📂**  <a id="2-4"></a>
```http
GET /_cat/indices?v
```
- Ye query **cluster ke sabhi indices** ko list karti hai.  
- Agar koi index nahi hai, toh system indices dekhne ke liye:  
```http
GET /_cat/indices?v&expand_wildcards=all
```
- System indices Elasticsearch aur Kibana ke internal operations ke liye hote hain.  

---

## 3️⃣ **Conclusion 🎯**  <a id="3"></a>
- **Cluster = Multiple nodes ka group jo data store aur manage karta hai.**  
- **Node = Elasticsearch ka ek instance jo ek machine pe multiple ho sakta hai.**  
- **Documents JSON format mein store hote hain aur Indices ke andar logically group kiye jate hain.**  
- **Cluster health, nodes, aur indices ko inspect karne ke liye Kibana Console ka use kar sakte hain.**  

### **Agle Steps:**  
1️⃣ **Kibana Console use karke in queries ko execute karo.**  
2️⃣ **Cluster health aur node info analyze karo.**  
3️⃣ **System indices aur unka purpose samjho.**  

