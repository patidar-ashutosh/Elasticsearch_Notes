**Elasticsearch me kuch in-built REST APIs hain** jo tumhare cluster, nodes, aur indices ka status check karne ke liye diye gaye hain.

### **📌 Elasticsearch APIs ka kaam kya hai?**  
Elasticsearch **RESTful APIs** provide karta hai jo tumhare cluster ke andar ka data manage karne aur search operations karne me madad karti hain.  

Ye APIs **default** Elasticsearch ke saath aati hain, tumhe alag se kuch install karne ki zaroorat nahi hoti.  

---

### **📌 Types of In-Built APIs in Elasticsearch**
1. **Cluster APIs** → Cluster aur nodes ka status check karne ke liye  
   - `GET /_cluster/health` (Cluster health check)  
   - `GET /_cat/nodes?v` (Node list)  

2. **Index APIs** → Index aur documents ko manage karne ke liye  
   - `GET /_cat/indices?v` (Saare indices ki list)  
   - `PUT /products` (Naya index banane ke liye)  
   - `DELETE /products` (Index delete karne ke liye)  

3. **Document APIs** → Documents insert, update aur delete karne ke liye  
   - `PUT /products/_doc/1` (Document insert/update)  
   - `GET /products/_doc/1` (Document retrieve)  
   - `DELETE /products/_doc/1` (Document delete)  

4. **Search APIs** → Data ko search karne ke liye  
   - `GET /products/_search?q=laptop` (Simple search)  
   - `POST /products/_search` (Advanced search with filters)  

---

## **📌_ (underscore) ka Matlab kya hai?**
- Elasticsearch me jo APIs underscore (_) se start hoti hain, wo built-in APIs hoti hain.
- Elasticsearch me _ se start hone wale endpoints internal ya built-in functionalities ko represent karte hain.
- Ye mostly cluster, index, search, settings, mapping jaise operations handle karne ke liye hote hain.
- Elasticsearch ka koi bhi core API _ se start hoti hai, lekin custom-defined endpoints me _ nahi hota.


---
## Some Example of Elasticsearch REST API commands

## **📌 1️⃣ `GET /_cluster/health` (Cluster Health Check)**
Ye command **poore Elasticsearch cluster ki health check** karti hai.  

🔹 Iska output **3 tarah ka ho sakta hai:**  
- 🟢 **`green`** → Sab kuch theek hai (Saare shards available hain).  
- 🟡 **`yellow`** → Primary shards available hain, lekin kuch replica shards missing hain.  
- 🔴 **`red`** → Kuch primary shards missing hain, cluster proper work nahi kar raha.  

✅ **Example Output:**
```json
{
  "cluster_name": "my-cluster",
  "status": "yellow",
  "number_of_nodes": 2,
  "number_of_data_nodes": 1,
  "active_primary_shards": 5,
  "active_shards": 10
}
```

---

## **📌 2️⃣ `GET /_cat/nodes?v` (Node Information)**
Ye command **cluster ke andar jitne bhi nodes hain unki details dikhati hai.**  

✅ **Example Output:**
```
ip           heap.percent cpu load_1m load_5m load_15m node.role   name
192.168.1.1  32          5   0.12    0.24    0.15      i          node-1
192.168.1.2  45          8   0.20    0.30    0.25      di         node-2
```
🔹 **heap.percent** → Memory usage  
🔹 **cpu** → CPU usage  
🔹 **node.role** → Node ka role (`i` = ingest, `d` = data, `m` = master, etc.)  
🔹 **name** → Node ka naam  

---

## **📌 3️⃣ `GET /_cat/indices?v&expand_wildcards=all` (Indices Information)**
Ye command **cluster ke andar jitne bhi indices hain unki details dikhati hai**.  

✅ **Example Output:**
```
health status index     uuid                   pri rep docs.count docs.deleted store.size
yellow open   products  Lkds8SdfKJShd8sdlf     1   1   5000       200          5mb
green  open   orders    8dsf7SDF89sdJKHsdj     2   2   10000      500          10mb
```
🔹 **health** → Index ki health (green/yellow/red)  
🔹 **status** → Index open hai ya closed  
🔹 **index** → Index ka naam  
🔹 **pri (primary shards)** → Kitne primary shards hain  
🔹 **rep (replica shards)** → Kitne replica shards hain  
🔹 **docs.count** → Kitne documents store hain  
🔹 **store.size** → Index ka total size  


✅ **Ye APIs Elasticsearch me already built-in hoti hain.**  
✅ **Tum inka use directly kar sakte ho bina kisi additional setup ke.**  
✅ **Elasticsearch ke saath interact karne ka primary way ye REST APIs hi hain.**  

---

### **📌 Elasticsearch API ko call karne ka syntax**  

Elasticsearch ke APIs **RESTful APIs** hoti hain, jo HTTP methods (`GET`, `POST`, `PUT`, `DELETE`) ka use karti hain.  

**Basic Syntax:**  
```http
<HTTP_METHOD> <BASE_URL>/<API_ENDPOINT>
```
🔹 **`HTTP_METHOD`** → API ka type (GET, POST, PUT, DELETE)  
🔹 **`BASE_URL`** → Tumhara Elasticsearch server (default: `http://localhost:9200`)  
🔹 **`API_ENDPOINT`** → API ka specific path  

---

## **📌 Example Commands**  

### **1️⃣ Cluster Health Check (GET Method)**
```bash
GET http://localhost:9200/_cluster/health
```
✅ Ye tumhare **Elasticsearch cluster** ki health check karega.  

---

### **2️⃣ Index Create Karna (PUT Method)**
```bash
PUT http://localhost:9200/products
```
✅ **"products"** naam ka ek naya index banayega.  

---

### **3️⃣ Document Insert Karna (PUT Method)**
```bash
PUT http://localhost:9200/products/_doc/1
Content-Type: application/json

{
  "name": "Laptop",
  "price": 50000
}
```
✅ **ID = 1** ke saath ek document insert karega.  

---

### **4️⃣ Document Fetch Karna (GET Method)**
```bash
GET http://localhost:9200/products/_doc/1
```
✅ ID **1** ka document retrieve karega.  

---

### **5️⃣ Document Delete Karna (DELETE Method)**
```bash
DELETE http://localhost:9200/products/_doc/1
```
✅ ID **1** ka document delete karega.  

---

### **📌 API Calls Postman ya cURL se kaise karein?**  

🔹 **Postman se**:  
1. **Method select karo** (`GET`, `PUT`, `POST`, `DELETE`).  
2. **URL me API likho** (`http://localhost:9200/_cat/indices?v`).  
3. **Body me JSON data add karo (agar required ho to)**.  
4. **Send button dabao** ✅  

🔹 **cURL se (Command Line me):**  
```bash
curl -X GET "http://localhost:9200/_cat/indices?v"
```
✅ Ye sab indices ki list dikhayega.  

---

## **🎯 Summary**  
✅ **Elasticsearch APIs HTTP methods use karti hain (GET, POST, PUT, DELETE).**  
✅ **Syntax: `<HTTP_METHOD> <BASE_URL>/<API_ENDPOINT>`**  
✅ **Postman ya cURL ka use karke APIs call kar sakte ho.**  

Agar aur detail chahiye toh batao bhai! 🚀🔥


| **Command** | **Kya karta hai?** |
|------------|------------------|
| `GET /_cluster/health` | Cluster ki health check karta hai |
| `GET /_cat/nodes?v` | Cluster ke nodes ki details dikhata hai |
| `GET /_cat/indices?v&expand_wildcards=all` | Cluster ke indices ki details dikhata hai |
