# **📌 Geo Point in OpenSearch**  

## **📌 Introduction**  
**Geo Point** ek **special data type** hai jo **latitude & longitude** ko store karne ke liye use hota hai.  
- Iska use **geographical search aur location-based queries** ke liye hota hai.  
- **Geo Distance, Bounding Box, GeoHash, Geo Aggregations** jaise features ke liye yeh mandatory hai.  

📌 **Use Cases:**  
✔ **Map-based Search** (Jaise ek city ke andar sabhi hotels dhoondhna).  
✔ **Nearby Location Queries** (Jaise ek shop ke 5 km radius me customers search karna).  
✔ **Geospatial Analytics** (Jaise kisi area me highest crime zones identify karna).  

---

## **📌 Syntax (Short & Simple)**  
Geo Point ko OpenSearch me **3 tariko se store kar sakte hain:**  

### **1️⃣ Object Format (Recommended)**
```json
{
  "location": { "lat": 40.748817, "lon": -73.985428 }
}
```

### **2️⃣ String Format**
```json
{
  "location": "40.748817, -73.985428"
}
```

### **3️⃣ Array Format**
```json
{
  "location": [ -73.985428, 40.748817 ]  // [lon, lat]
}
```
✔ **Object Format recommended hai kyunki yeh readable aur easy to use hota hai.**  

---

## **✅ Example: Create Index with `geo_point` Field**  
```json
PUT places
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "location": { "type": "geo_point" }
    }
  }
}
```
✔ **Yeh ensure karega ki `location` field properly index ho jaye.**  

---

## **✅ Example: Insert Geo Data**
```json
POST places/_bulk
{ "index": { "_id": 1 } }
{ "name": "Statue of Liberty", "location": { "lat": 40.689247, "lon": -74.044502 } }
{ "index": { "_id": 2 } }
{ "name": "Empire State Building", "location": { "lat": 40.748817, "lon": -73.985428 } }
{ "index": { "_id": 3 } }
{ "name": "Central Park", "location": { "lat": 40.785091, "lon": -73.968285 } }
```
✔ **Yeh 3 locations store karega jisme `geo_point` use ho raha hai.**  

---

## **✅ Example: Geo Point Search (Find Places in a 10 km Radius)**  
```json
GET places/_search
{
  "query": {
    "geo_distance": {
      "distance": "10km",
      "location": {
        "lat": 40.758896,
        "lon": -73.985130
      }
    }
  }
}
```
✔ **Yeh `"10 km"` ke radius me sabhi locations return karega.**  

---

## **📌 Key Takeaways**  
✔ **`geo_point` geographical data ko store aur search karne ke liye use hota hai.**  
✔ **3 formats me store kar sakte hain: Object, String, Array.**  
✔ **Location-based queries jaise Geo Distance, Bounding Box, GeoHash ke liye `geo_point` mandatory hai.**  
✔ **Agar polygon-based search karni ho to `geo_shape` better hai.**  

