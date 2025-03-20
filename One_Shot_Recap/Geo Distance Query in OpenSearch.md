# **📌 Geo Distance Query in OpenSearch**  

## **📌 Introduction**  
Geo Distance Query ka use **ek fixed distance ke andar documents filter karne** ke liye hota hai.  
- Yeh **latitude & longitude** ka use karke **circular search area** define karta hai.  
- **Agar koi document is defined radius ke andar aata hai, to woh match karega.**  

📌 **Use Cases:**  
✔ **Nearby Places Search** (Jaise `10 km` ke radius me sabhi restaurants dhoondhna).  
✔ **Delivery Service Radius** (Jaise ek location se `5 km` ke andar customers filter karna).  
✔ **Geofencing Alerts** (Jaise kisi user ke ek specific area me enter hone par alert dena).  

---

## **📌 Syntax (Short & Simple)**  
```json
GET index_name/_search
{
  "query": {
    "geo_distance": {
      "distance": "X km",
      "location_field": {
        "lat": latitude,
        "lon": longitude
      }
    }
  }
}
```
🔹 **index_name** → Jis index me search karna hai.  
🔹 **distance** → Radius define karta hai (Jaise `"10km"`, `"5mi"`).  
🔹 **location_field** → Jisme `geo_point` type ka data store hai.  
🔹 **lat/lon** → Jis location se distance measure karna hai.  

---

## **✅ Example: Find Restaurants Within 10 km**  
### **Step 1: Create Index with `geo_point` Field**
```json
PUT restaurants
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "location": { "type": "geo_point" }
    }
  }
}
```
---

### **Step 2: Insert Sample Data**
```json
POST restaurants/_bulk
{ "index": { "_id": 1 } }
{ "name": "Pizza Palace", "location": { "lat": 40.748817, "lon": -73.985428 } }
{ "index": { "_id": 2 } }
{ "name": "Burger House", "location": { "lat": 40.741895, "lon": -73.989308 } }
{ "index": { "_id": 3 } }
{ "name": "Sushi Spot", "location": { "lat": 40.730610, "lon": -73.935242 } }
```
---

### **Step 3: Search Restaurants Within 10 km of a Given Location**
Agar hume **New York Times Square (40.758896, -73.985130)** ke **10 km radius** me sabhi restaurants dhoondhne hain:
```json
GET restaurants/_search
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
---

## **📌 Expected Output**
```json
{
  "hits": {
    "hits": [
      { "_source": { "name": "Pizza Palace", "location": { "lat": 40.748817, "lon": -73.985428 } } },
      { "_source": { "name": "Burger House", "location": { "lat": 40.741895, "lon": -73.989308 } } }
    ]
  }
}
```
✔ **Sirf wahi restaurants return honge jo `10 km` ke andar aate hain.**  

---

## **📌 Key Takeaways**  
✔ **Geo Distance query circular search ke liye best hai.**  
✔ **`distance` unit `"km"`, `"mi"`, `"m"`, `"yd"` ho sakti hai.**  
✔ **Speed optimization ke liye `geo_point` field index hona chahiye.**  
✔ **Agar rectangular area chahiye to `geo_bounding_box` better hai.**  

