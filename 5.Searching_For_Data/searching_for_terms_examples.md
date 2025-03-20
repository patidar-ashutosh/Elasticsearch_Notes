## **Example 1: Term Query on Keyword Field**  
Chalo, maan lo humare paas ek `products` index hai, jisme documents kuch is tarah se hain:  

### **Sample Documents in `products` index**
```json
{
  "name": "Carrot",
  "tags": ["Vegetable", "Healthy"],
  "price": 20,
  "active": true
}
```
```json
{
  "name": "Chicken",
  "tags": ["Meat", "Protein"],
  "price": 150,
  "active": true
}
```
```json
{
  "name": "Tomato Soup",
  "tags": ["Soup", "Vegetable"],
  "price": 50,
  "active": false
}
```

### **Query: Search for Products with "Vegetable" Tag**
```json
GET products/_search
{
  "query": {
    "term": {
      "tags.keyword": {
        "value": "Vegetable"
      }
    }
  }
}
```

### **Expected Output**
```json
{
  "hits": {
    "total": 1,
    "hits": [
      {
        "_source": {
          "name": "Carrot",
          "tags": ["Vegetable", "Healthy"],
          "price": 20,
          "active": true
        }
      }
    ]
  }
}
```
➡ **Samajhne ki baat:**  
- Query `"Vegetable"` ka match karega **sirf "tags.keyword"** field par, kyunki ye exact match karta hai.
- Agar hum `"tags"` likhte bina `.keyword`, to query analyze hoti aur shayad match na karti.

---

## **Example 2: Case Sensitivity Issue**
Agar hum lowercase `"vegetable"` search karein, to kya hoga?

### **Query**
```json
GET products/_search
{
  "query": {
    "term": {
      "tags.keyword": {
        "value": "vegetable"
      }
    }
  }
}
```

### **Expected Output**
```json
{
  "hits": {
    "total": 0,
    "hits": []
  }
}
```
➡ **Samajhne ki baat:**  
- Kyunki `"Vegetable"` aur `"vegetable"` alag alag hain (case-sensitive match hota hai), query match nahi karegi.

---

## **Example 3: Case-Insensitive Search**
Agar case-insensitive search karna hai, to hum `case_insensitive: true` use kar sakte hain.

### **Query**
```json
GET products/_search
{
  "query": {
    "term": {
      "tags.keyword": {
        "value": "vegetable",
        "case_insensitive": true
      }
    }
  }
}
```

### **Expected Output**
```json
{
  "hits": {
    "total": 1,
    "hits": [
      {
        "_source": {
          "name": "Carrot",
          "tags": ["Vegetable", "Healthy"],
          "price": 20,
          "active": true
        }
      }
    ]
  }
}
```
➡ **Samajhne ki baat:**  
- `"Vegetable"` aur `"vegetable"` dono ko same maana jayega.

---

## **Example 4: Searching for Multiple Terms (terms Query)**
Agar hume `"Meat"` ya `"Soup"` wale products dhoondhne hain, to `terms` query ka use karenge.

### **Query**
```json
GET products/_search
{
  "query": {
    "terms": {
      "tags.keyword": ["Meat", "Soup"]
    }
  }
}
```

### **Expected Output**
```json
{
  "hits": {
    "total": 2,
    "hits": [
      {
        "_source": {
          "name": "Chicken",
          "tags": ["Meat", "Protein"],
          "price": 150,
          "active": true
        }
      },
      {
        "_source": {
          "name": "Tomato Soup",
          "tags": ["Soup", "Vegetable"],
          "price": 50,
          "active": false
        }
      }
    ]
  }
}
```
➡ **Samajhne ki baat:**  
- Yeh query **"Meat"** ya **"Soup"** tag wale products ko match karegi.
- Dono tags wale documents results me aayenge.

---

