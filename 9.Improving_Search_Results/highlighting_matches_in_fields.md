# 📌 **Highlighting Matches in Fields in Elasticsearch**  

## 📖 **Table of Contents**  
1. 🔍 **Highlighting in Search Engines**  
2. ⚡ **Elasticsearch Highlighter Overview**  
3. 🛠 **Basic Example of Highlighting**  
4. 🎭 **How Elasticsearch Generates Highlights**  
5. 🔄 **Synonyms & Stemming in Highlighting**  
6. 🎨 **Customizing Highlight Tags**  
7. 🏁 **Conclusion & Final Thoughts**  

---

## 🔍 **1. Highlighting in Search Engines**  
Jab bhi tum Google, Bing ya kisi bhi search engine me kuch search karte ho, to matched text **bold ya highlight** ho jata hai. Ye feature **Elasticsearch me bhi available hai!**  

🔹 **Example:**  
Agar tum `"Elasticsearch tutorial"` search kar rahe ho, aur result me `"Elasticsearch"` highlighted dikhe, to ye highlighting ka use hai.

---

## ⚡ **2. Elasticsearch Highlighter Overview**  
**Elasticsearch me highlighting ka use highlighter object se hota hai.**  

💡 **Why Use Highlighter?**  
✅ Matched text ko easily identify karne ke liye.  
✅ Long documents me specific matches dikhane ke liye.  
✅ Synonyms aur stemming ko properly handle karne ke liye.  

🔹 **Highlighter ko enable karne ke liye tumhe `highlight` object add karna hoga.**  
Elasticsearch **default** me `"plain"` highlighter use karta hai, jo most cases me sufficient hota hai.

---

## 🛠 **3. Basic Example of Highlighting**  
Chalo ek **basic example** dekhte hai highlighting ka.

### 🔧 **Step 1: Index a Sample Document**  
Pehle ek document add karte hain:

```json
PUT my_index/_doc/1
{
  "description": "Elasticsearch is a powerful search engine. This Elasticsearch tutorial covers basics."
}
```

### 🔍 **Step 2: Search with Highlighting**  
Ab search query likhte hain **highlighted matches** ke saath:

```json
POST my_index/_search
{
  "query": {
    "match": {
      "description": "Elasticsearch tutorial"
    }
  },
  "highlight": {
    "fields": {
      "description": {}
    }
  }
}
```

### 📜 **Expected Output:**  
```json
{
  "hits": {
    "hits": [
      {
        "_source": {
          "description": "Elasticsearch is a powerful search engine. This Elasticsearch tutorial covers basics."
        },
        "highlight": {
          "description": [
            "<em>Elasticsearch</em> is a powerful search engine. This <em>Elasticsearch</em> tutorial covers basics."
          ]
        }
      }
    ]
  }
}
```

💡 **Notice:** `"Elasticsearch"` aur `"tutorial"` `<em>` tags ke andar aa gaya hai, jo browser me italicized dikhega.

---

## 🎭 **4. How Elasticsearch Generates Highlights**  
🔹 Elasticsearch **documents ke inverted index me jaake matching fragments fetch karta hai.**  
🔹 **Long documents** hone par pura text nahi dikhata, **sirf matched parts (fragments) dikhata hai.**  
🔹 **Default me `<em>` tag ka use hota hai**, jisse tum easily highlight kar sako.

---

## 🔄 **5. Synonyms & Stemming in Highlighting**  
**Synonyms aur stemming ka impact bhi hota hai highlights par.**  

### **Example with Synonyms**
Agar tumhara synonym file me yeh ho:  
```
happy, delighted
```
Aur document index kiya gaya ho:
```json
PUT my_index/_doc/2
{
  "description": "I am feeling very happy today!"
}
```
Aur tum search kar rahe ho `"delighted"` se:
```json
POST my_index/_search
{
  "query": {
    "match": { "description": "delighted" }
  },
  "highlight": {
    "fields": { "description": {} }
  }
}
```

### 📜 **Expected Output:**  
```json
{
  "hits": {
    "hits": [
      {
        "_source": {
          "description": "I am feeling very happy today!"
        },
        "highlight": {
          "description": [
            "I am feeling very <em>happy</em> today!"
          ]
        }
      }
    ]
  }
}
```

💡 **Even though we searched for "delighted", Elasticsearch ne "happy" highlight kar diya kyunki wo synonym file me mapped tha!**

---

## 🎨 **6. Customizing Highlight Tags**  
By default, highlighted text `<em>` tags me wrap hota hai. Tum isko change bhi kar sakte ho.

### **Example: Custom Tags**
```json
POST my_index/_search
{
  "query": {
    "match": {
      "description": "Elasticsearch tutorial"
    }
  },
  "highlight": {
    "pre_tags": ["<strong>"],
    "post_tags": ["</strong>"],
    "fields": {
      "description": {}
    }
  }
}
```

### 📜 **Expected Output:**  
```json
{
  "hits": {
    "hits": [
      {
        "_source": {
          "description": "Elasticsearch is a powerful search engine. This Elasticsearch tutorial covers basics."
        },
        "highlight": {
          "description": [
            "<strong>Elasticsearch</strong> is a powerful search engine. This <strong>Elasticsearch</strong> tutorial covers basics."
          ]
        }
      }
    ]
  }
}
```

📝 **Customization Options:**  
✅ **Bold text** ke liye `<strong>` tag use kar sakte ho.  
✅ **CSS styling** ke liye `<span class='highlight'>` tag ka use kar sakte ho.  

---

## 🏁 **7. Conclusion & Final Thoughts**  
✅ **Highlighting feature search results ko readable aur user-friendly banata hai.**  
✅ **Synonyms aur stemming ko Elasticsearch automatically handle karta hai, jo manually handle karna difficult hota.**  
✅ **Different highlighter types available hain**, jo complex scenarios me kaam aate hain.  
✅ **Custom HTML tags** use karke highlights ko UI ke hisaab se modify kiya ja sakta hai.  

🔥 **Pro Tip:** Agar tum advanced highlighting chahte ho, to `fvh` (Fast Vector Highlighter) aur `unified` highlighter explore kar sakte ho!  

---

