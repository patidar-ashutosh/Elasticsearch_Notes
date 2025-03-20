# **📌 Geo Shape in OpenSearch**  

## **📌 Introduction**  
**Geo Shape** ek **advanced data type** hai jo complex geographical shapes jaise **polygons, multipoints, lines, circles, aur envelopes** ko store aur query karne ke liye use hota hai.  
- **Geo Point sirf ek location ko store karta hai**, lekin **Geo Shape multiple points aur complex areas** ko define kar sakta hai.  
- **Geofencing, route matching, aur area-based searches** ke liye useful hai.  

📌 **Use Cases:**  
✔ **Geofencing** (Jaise ek city ke andar users track karna).  
✔ **Route Planning** (Jaise kisi road network par path find karna).  
✔ **Custom Region Search** (Jaise kisi country ke andar sabhi stores dhoondhna).  

---

## **📌 Supported Shapes**  
OpenSearch **GeoJSON format** follow karta hai. Yeh shapes support karta hai:  

| **Shape Type**  | **Description** |
|-----------------|---------------|
| **Point**       | Ek single location |
| **MultiPoint**  | Multiple points ka set |
| **LineString**  | Ek line jo multiple points ko connect karti hai |
| **MultiLineString** | Multiple line sets |
| **Polygon**     | Ek closed area (boundary) |
| **MultiPolygon** | Multiple polygons |
| **Envelope**    | Ek rectangle (Top-Left to Bottom-Right) |
| **Circle**      | Ek radius-based shape |

---

## **📌 Syntax (Short & Simple)**  
```json
PUT index_name
{
  "mappings": {
    "properties": {
      "location": { "type": "geo_shape" }
    }
  }
}
```
🔹 **index_name** → Jisme data store karna hai.  
🔹 **location** → **geo_shape type ka field**, jo complex shapes ko store karega.  

---

## **✅ Example 1: Store a Polygon (New York City Boundary)**  
```json
POST cities/_doc/1
{
  "name": "New York City",
  "location": {
    "type": "polygon",
    "coordinates": [
      [
        [-74.25909, 40.477399], 
        [-73.700272, 40.477399], 
        [-73.700272, 40.917577], 
        [-74.25909, 40.917577], 
        [-74.25909, 40.477399]
      ]
    ]
  }
}
```
✔ **Yeh ek Polygon store karega jo New York City ka boundary define karta hai.**  

---

## **✅ Example 2: Search Locations Inside a Polygon**  
```json
GET cities/_search
{
  "query": {
    "geo_shape": {
      "location": {
        "shape": {
          "type": "polygon",
          "coordinates": [
            [
              [-74.25909, 40.477399], 
              [-73.700272, 40.477399], 
              [-73.700272, 40.917577], 
              [-74.25909, 40.917577], 
              [-74.25909, 40.477399]
            ]
          ]
        },
        "relation": "intersects"
      }
    }
  }
}
```
✔ **Yeh query sirf wahi cities return karegi jo is polygon ke andar aati hain.**  
✔ **Relation `"intersects"` ka matlab hai koi bhi point jo boundary ko touch kare ya uske andar ho, match hoga.**  

---

## **✅ Example 3: Store a Circle (5 km radius around a point)**  
```json
POST areas/_doc/1
{
  "name": "Restricted Zone",
  "location": {
    "type": "circle",
    "coordinates": [-73.985428, 40.748817],
    "radius": "5km"
  }
}
```
✔ **Yeh `"5 km"` radius ka ek circular geofence store karega.**  

---

## **📌 Key Takeaways**  
✔ **`geo_shape` complex geographical shapes (polygons, circles, lines) store karne ke liye use hota hai.**  
✔ **Geospatial queries jaise `intersects`, `within`, `disjoint` se area-based searches fast ho jati hain.**  
✔ **Agar sirf single location store karni ho to `geo_point` better hai.**  
✔ **Geofencing aur route-based searches ke liye `geo_shape` best hai.**  

