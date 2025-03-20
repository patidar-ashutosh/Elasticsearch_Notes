# **📌 Bounding Box Search in OpenSearch**  

## **📌 Introduction**  
Bounding Box Search ka use **geographical location-based search** ke liye hota hai.  
- Yeh **latitude & longitude** values ka use karke ek rectangular area define karta hai.  
- **Is bounded area ke andar jo documents aate hain, wahi return hote hain.**  

📌 **Use Cases:**  
✔ **Map-based filtering** (Jaise ek specific city ya region me businesses dhoondhna).  
✔ **Nearby search** (Jaise `10 km radius` ke andar restaurants dikhana).  
✔ **Geospatial data analysis** (Jaise kisi area me crime rate analysis).  

---

## **📌 Syntax (Short & Simple)**  
```json
GET index_name/_search
{
  "query": {
    "geo_bounding_box": {
      "location_field": {
        "top_left": {
          "lat": max_latitude,
          "lon": min_longitude
        },
        "bottom_right": {
          "lat": min_latitude,
          "lon": max_longitude
        }
      }
    }
  }
}
```
🔹 **index_name** → Jis index me search karna hai.  
🔹 **location_field** → Jisme latitude-longitude store hai.  
🔹 **top_left** → Bounding box ka top-left coordinate.  
🔹 **bottom_right** → Bounding box ka bottom-right coordinate.  

---

## **✅ Example: Restaurants Within a City**  
Hum ek index **restaurants** create karenge jisme location field hoga.  

### **Step 1: Index & Data Creation**
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

### **✅ Bounding Box Query**
Hum **New York City ke ek specific rectangular area** me restaurants search karenge.  

```json
GET restaurants/_search
{
  "query": {
    "geo_bounding_box": {
      "location": {
        "top_left": { "lat": 40.75, "lon": -74.00 },
        "bottom_right": { "lat": 40.73, "lon": -73.93 }
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
      { "_source": { "name": "Pizza Palace", "location": { "lat": 40.748817, "lon": -73.985428 } } },
      { "_source": { "name": "Burger House", "location": { "lat": 40.741895, "lon": -73.989308 } } }
    ]
  }
}
```
✔ **Sirf wahi restaurants return honge jo defined bounding box ke andar aate hain.**  

---

## **📌 Key Takeaways**  
✔ **Bounding Box Search location-based filtering ke liye hoti hai.**  
✔ **`geo_point` field mandatory hai location data store karne ke liye.**  
✔ **`top_left` aur `bottom_right` coordinates se rectangular area define hota hai.**  
✔ **Agar circular search chahiye to `geo_distance` query better hai.**  
