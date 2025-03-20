# **Understanding Analysis in Elasticsearch**  

## **Table of Contents**  
1. [Introduction to Analysis](#introduction-to-analysis)  
2. [Why Analysis is Needed](#why-analysis-is-needed)  
3. [How Elasticsearch Processes Text](#how-elasticsearch-processes-text)  
4. [Understanding Analyzers](#understanding-analyzers)  
   - [Character Filters](#character-filters)  
   - [Tokenizers](#tokenizers)  
   - [Token Filters](#token-filters)  
5. [How Default Analysis Works](#how-default-analysis-works)  
6. [Standard Analyzer](#standard-analyzer)  
7. [Other Built-in Analyzers](#other-built-in-analyzers)  
8. [Creating Custom Analyzers](#creating-custom-analyzers)  
9. [Examples of Analysis in Kibana](#examples-of-analysis-in-kibana)  
10. [Conclusion](#conclusion)  

---

## **1️⃣ Introduction to Analysis**  
Elasticsearch me *Analysis* ka matlab hai text values ko process karna taaki unhe efficiently search kiya ja sake. Is process ko *Text Analysis* bhi kaha jata hai kyunki yeh sirf text fields ke liye apply hota hai.  

Jab bhi hum kisi text ko index karte hain, uska ek analysis process hota hai jo Elasticsearch ke internal data structure ke according hota hai.  

---

## **2️⃣ Why Analysis is Needed?**  
Jab hum Elasticsearch me data store karte hain, toh hum jo original text bhejte hain, woh *"_source"* field me save hota hai.  

Lekin actual search queries ke time, Elasticsearch in original values ko use nahi karta. Kyunki ek bada text efficiently search karne ke liye process karna zaroori hota hai.  

Example:  
Agar hum ek product description store karein:  
```
"The best Coffee Maker in the market!"  
```
Toh bina analysis ke, Elasticsearch ko yeh samajhna mushkil hoga ki hum `"coffee"` ya `"maker"` search karein toh yeh document mile.  

Isliye Elasticsearch text ko process karta hai taaki fast aur accurate searching ho sake.  

---

## **3️⃣ How Elasticsearch Processes Text?**  
Jab bhi hum ek *text* field ko index karte hain, Elasticsearch ek *Analyzer* ka use karta hai jo us text ko analyze karta hai.  

🔹 *Analyzer* ke **3 main parts** hote hain:  
1. **Character Filters** - Text ke characters ko modify karta hai  
2. **Tokenizer** - Text ko chhoti units (tokens) me todta hai  
3. **Token Filters** - Tokens par aur processing karta hai  

Yeh sab milkar ek efficient searchable format create karte hain.  

---

## **4️⃣ Understanding Analyzers**  
### **📌 Character Filters**  
✔️ Yeh step original text ko modify karta hai before tokenization.  
✔️ Text me se unwanted characters remove kiye ja sakte hain ya replace kiye ja sakte hain.  

Example:  
Agar ek document me HTML elements hain:  
```
<p>Buy the <b>best</b> coffee maker!</p>
```
Agar hum `"html_strip"` character filter use karein toh output hoga:  
```
Buy the best coffee maker!
```
🔹 **Common Character Filters:**  
- `html_strip` → HTML tags remove karta hai  
- `mapping` → Specific characters ko replace karta hai  

---

### **📌 Tokenizer**  
✔️ Yeh step text ko *tokens* me todta hai.  
✔️ Tokenization ka matlab hai text ko chhoti units (words) me convert karna.  

Example:  
Text: `"Coffee-Maker is amazing!"`  
Agar `"standard"` tokenizer use ho, toh output tokens honge:  
```
["Coffee", "Maker", "is", "amazing"]
```
🔹 **Common Tokenizers:**  
- `standard` → Unicode segmentation ke basis pe split karta hai  
- `whitespace` → Sirf spaces pe split karta hai  
- `keyword` → Pura text ek hi token me store hota hai  

---

### **📌 Token Filters**  
✔️ Yeh tokens pe further processing karta hai jaise lowercase conversion, stopwords removal, stemming, etc.  

Example:  
Agar text:  
```
"COFFEE MAKER is amazing!"
```
Aur `"lowercase"` token filter use ho, toh output tokens honge:  
```
["coffee", "maker", "is", "amazing"]
```
🔹 **Common Token Filters:**  
- `lowercase` → Sab words ko lowercase me convert karta hai  
- `stopwords` → Common words jaise `"is", "the", "a"` ko remove karta hai  
- `stemmer` → Words ke base form ko extract karta hai (`running` → `run`)  

---

## **5️⃣ How Default Analysis Works?**  
Jab Elasticsearch ek text field ko index karta hai, toh uske default analysis process me yeh steps hote hain:  
1. **Character Filter** → (By default, koi nahi hota)  
2. **Tokenizer** → `standard` tokenizer use hota hai  
3. **Token Filter** → `lowercase` filter apply hota hai  

Example:  
```
"The Quick Brown Fox Jumps Over The Lazy Dog."
```
Analysis ke baad tokens honge:  
```
["the", "quick", "brown", "fox", "jumps", "over", "the", "lazy", "dog"]
```
✔️ **Punctuation remove ho gayi**  
✔️ **Words lowercase ho gaye**  

---

## **6️⃣ Standard Analyzer**  
✔️ Yeh Elasticsearch ka default analyzer hai.  
✔️ Yeh **standard tokenizer** aur **lowercase token filter** use karta hai.  

Example:  
```
"This is a Coffee Maker!"
```
Tokens honge:  
```
["this", "is", "a", "coffee", "maker"]
```

---

## **7️⃣ Other Built-in Analyzers**  
Apart from *Standard Analyzer*, Elasticsearch me aur bhi built-in analyzers available hain:  

| Analyzer | Description |
|----------|------------|
| `simple` | Sirf lowercase conversion karta hai |
| `whitespace` | Whitespace pe split karta hai, punctuation remove nahi karta |
| `stop` | Stopwords remove karta hai |
| `keyword` | Pura text ek hi token me store karta hai |
| `pattern` | Custom regex pattern ke basis pe tokenize karta hai |

Example for `"whitespace"` analyzer:  
```
"This-is a Coffee-Maker!"
```
Tokens honge:  
```
["This-is", "a", "Coffee-Maker!"]
```

---

## **8️⃣ Creating Custom Analyzers**  
Hum apna custom analyzer bhi bana sakte hain by combining:  
✔️ Character Filters  
✔️ Tokenizers  
✔️ Token Filters  

Example: Custom analyzer jo:  
- HTML remove kare  
- Lowercase kare  
- Stopwords remove kare  

```json
PUT /custom_index
{
  "settings": {
    "analysis": {
      "analyzer": {
        "custom_analyzer": {
          "type": "custom",
          "char_filter": ["html_strip"],
          "tokenizer": "standard",
          "filter": ["lowercase", "stop"]
        }
      }
    }
  }
}
```

🔹 **Test the Custom Analyzer**  
```json
GET custom_index/_analyze
{
  "analyzer": "custom_analyzer",
  "text": "<p>The Best Coffee Maker in the Market!</p>"
}
```

🔹 **Response:**  
```json
{
  "tokens": [
    { "token": "best", "start_offset": 4, "end_offset": 8, "type": "<ALPHANUM>", "position": 1 },
    { "token": "coffee", "start_offset": 9, "end_offset": 15, "type": "<ALPHANUM>", "position": 2 },
    { "token": "maker", "start_offset": 16, "end_offset": 21, "type": "<ALPHANUM>", "position": 3 },
    { "token": "market", "start_offset": 25, "end_offset": 31, "type": "<ALPHANUM>", "position": 4 }
  ]
}
```
✔️ `"The"` aur `"in"` stopwords remove ho gaye  
✔️ HTML tags remove ho gaye  
✔️ Lowercase apply ho gaya  

---

## **9️⃣ Examples of Analysis in Kibana**  
### **Example 1: Standard Analyzer (Default)**
Jab hum `"standard"` analyzer ka use karte hain, toh yeh text ko tokenize karta hai aur lowercase bhi karta hai.  

🔹 **Query in Kibana Dev Tools:**  
```json
GET _analyze
{
  // "analyzer": "standard",
  "char_filter": [],
  "tokenizer": "standard",
  "filter": ["lowercase"],
  "text": "The Quick Brown Fox Jumps Over The Lazy Dog!"
}
```
🔹 **Response:**  
```json
{
  "tokens": [
    { "token": "the", "start_offset": 0, "end_offset": 3, "type": "<ALPHANUM>", "position": 0 },
    { "token": "quick", "start_offset": 4, "end_offset": 9, "type": "<ALPHANUM>", "position": 1 },
    { "token": "brown", "start_offset": 10, "end_offset": 15, "type": "<ALPHANUM>", "position": 2 },
    { "token": "fox", "start_offset": 16, "end_offset": 19, "type": "<ALPHANUM>", "position": 3 },
    { "token": "jumps", "start_offset": 20, "end_offset": 25, "type": "<ALPHANUM>", "position": 4 },
    { "token": "over", "start_offset": 26, "end_offset": 30, "type": "<ALPHANUM>", "position": 5 },
    { "token": "the", "start_offset": 31, "end_offset": 34, "type": "<ALPHANUM>", "position": 6 },
    { "token": "lazy", "start_offset": 35, "end_offset": 39, "type": "<ALPHANUM>", "position": 7 },
    { "token": "dog", "start_offset": 40, "end_offset": 43, "type": "<ALPHANUM>", "position": 8 }
  ]
}
```
✔️ Punctuation remove ho gaya  
✔️ Words lowercase ho gaye  

---

### **Example 2: Whitespace Analyzer**
Agar hum `"whitespace"` analyzer use karein, toh yeh sirf whitespaces pe tokenize karega, aur punctuation **remove nahi karega**.  

🔹 **Query:**  
```json
GET _analyze
{
  "analyzer": "whitespace",
  "text": "Coffee-Maker is Amazing!"
}
```
🔹 **Response:**  
```json
{
  "tokens": [
    { "token": "Coffee-Maker", "start_offset": 0, "end_offset": 12, "type": "word", "position": 0 },
    { "token": "is", "start_offset": 13, "end_offset": 15, "type": "word", "position": 1 },
    { "token": "Amazing!", "start_offset": 16, "end_offset": 24, "type": "word", "position": 2 }
  ]
}
```
✔️ `"Coffee-Maker"` ek hi token hai  
✔️ `"Amazing!"` ka `"!"` remove nahi hua  

---

## **🔟 Conclusion**  
✔️ *Analysis* ka purpose hai text ko efficiently searchable format me store karna.  
✔️ Elasticsearch ke analyzers 3 parts me divide hote hain:  
   - Character Filters  
   - Tokenizers  
   - Token Filters  
✔️ *Standard Analyzer* default hota hai, but hum custom analyzers bhi bana sakte hain.  
✔️ Different use-cases ke liye multiple built-in analyzers available hain.  

---
[Reference Video Link](https://youtu.be/9OGyVzUGh1o?si=Va0NUFbvPqtVttpo)
---