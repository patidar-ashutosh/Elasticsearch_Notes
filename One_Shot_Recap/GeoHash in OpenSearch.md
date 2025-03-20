# **📌 GeoHash in OpenSearch**  

## **📌 Introduction**  
GeoHash ek **string-based representation hota hai latitude & longitude values ka**, jo **spatial indexing aur fast geo-search** ke liye use hota hai.  
- Yeh **grid-based encoding** karta hai, jisme **chhoti strings ka bada area represent karna** possible hota hai.  
- **GeoHash jitna bada hoga, utna hi precise location hogi.**  

📌 **Use Cases:**  
✔ **Fast Geo-Search & Filtering** (Jaise ek region ke andar sabhi locations dhoondhna).  
✔ **Clustering & Grouping** (Jaise nearby cities ko ek hi group me rakhna).  
✔ **Map-based applications** (Jaise OpenStreetMap aur GPS tracking).  

---

## **📌 GeoHash Working**  
GeoHash ek **alphanumeric string** generate karta hai, jo **latitude-longitude coordinates** ko represent karta hai.  
- **Chhoti string → Large area cover karegi (Less precision).**  
- **Badi string → Chhoti area cover karegi (More precision).**  

### **✅ Example: GeoHash Levels**
| **GeoHash String** | **Approximate Area Covered** |
|------------------|------------------------|
| `"dr5"`          | ~500 km × 500 km       |
| `"dr5r"`         | ~39 km × 19.5 km       |
| `"dr5ru"`        | ~4.9 km × 4.9 km       |
| `"dr5ru6"`       | ~610 m × 610 m         |

✔ **Agar hume more precision chahiye, to GeoHash ki length badhayein.**  

---

## **📌 Syntax (Short & Simple)**  
```json
GET index_name/_search
{
  "query": {
    "geo_bounding_box": {
      "geo_field": {
        "geohash": "geohash_string"
      }
    }
  }
}
```
🔹 **index_name** → Jis index me search karna hai.  
🔹 **geo_field** → Jisme latitude-longitude store hai (`geo_point` type).  
🔹 **geohash_string** → Jo location ke geohash ke basis pe filtering karega.  

---

## **✅ Example: Creating an Index with GeoHash**  
Agar hum ek **restaurants index** create karein jisme **geohash_enabled ho**, to:
```json
PUT restaurants
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "location": { 
        "type": "geo_point",
        "geohash": true
      }
    }
  }
}
```
✔ **`geo_point` field me `geohash: true` set karne se OpenSearch automatically geohash generate karega.**  

---

## **✅ Example: GeoHash Query (Find Restaurants in GeoHash Area)**  
```json
GET restaurants/_search
{
  "query": {
    "geo_bounding_box": {
      "location": {
        "geohash": "dr5ru"
      }
    }
  }
}
```
✔ **Yeh sirf wahi locations return karega jo `"dr5ru"` region me aati hain.**  

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
✔ **Sirf `"dr5ru"` region me aane wale restaurants return honge.**  

---

## **📌 Key Takeaways**  
✔ **GeoHash fast spatial search aur indexing ke liye use hota hai.**  
✔ **Chhoti GeoHash string large area represent karti hai, badi GeoHash string chhoti area represent karti hai.**  
✔ **GeoHash-based filtering se location-based queries fast ho jati hain.**  
✔ **Agar exact latitude-longitude matching chahiye to `geo_distance` query better hai.**  

