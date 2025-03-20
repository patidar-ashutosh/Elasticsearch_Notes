# 📊 **Using the Analyze API in Elasticsearch**  

## 📌 **Table of Contents**  
1. 🔍 **Introduction to Analyze API**  
2. 🛠️ **How to Use the Analyze API**  
3. 🏗️ **Understanding the Standard Analyzer**  
4. 🔄 **Tokenization Process**  
5. 🏷️ **Metadata in Analyze API Response**  
6. 🧑‍🔬 **Custom Analyzers Breakdown**  
7. 📝 **Example Queries & Expected Output**  
8. 🎯 **Key Takeaways**  

---  

## 🔍 **1. Introduction to Analyze API**  
Analyze API **Elasticsearch me ek debugging tool hai** jo tumhe batata hai ki **koi string ka analysis kaise hoga**. Iska use mainly **custom analyzers banane ya existing analyzers ko samajhne** ke liye hota hai.  

Is API se tum **tokens, token types, offsets, aur filters** ka effect dekh sakte ho. **Production me custom analyzers create karne se pehle yeh API kaafi useful hoti hai!** 🚀  

---  

## 🛠️ **2. How to Use the Analyze API**  
Elasticsearch me Analyze API use karne ka **endpoint** hai:  

```
POST /_analyze
```

Isme tum **"text" parameter me apna input string doge** aur **"analyzer" parameter specify kar sakte ho** agar tum kisi specific analyzer se analysis karwana chahte ho.  

**Example Query (Standard Analyzer)**:  
```json
POST _analyze
{
  "analyzer": "standard",
  "text": "The Quick Brown Fox Jumps Over 3 Lazy Dogs!"
}
```

**Expected Output**:
```json
{
  "tokens": [
    {"token": "the", "start_offset": 0, "end_offset": 3, "type": "<ALPHANUM>", "position": 0},
    {"token": "quick", "start_offset": 4, "end_offset": 9, "type": "<ALPHANUM>", "position": 1},
    {"token": "brown", "start_offset": 10, "end_offset": 15, "type": "<ALPHANUM>", "position": 2},
    {"token": "fox", "start_offset": 16, "end_offset": 19, "type": "<ALPHANUM>", "position": 3},
    {"token": "jumps", "start_offset": 20, "end_offset": 25, "type": "<ALPHANUM>", "position": 4},
    {"token": "over", "start_offset": 26, "end_offset": 30, "type": "<ALPHANUM>", "position": 5},
    {"token": "3", "start_offset": 31, "end_offset": 32, "type": "<NUM>", "position": 6},
    {"token": "lazy", "start_offset": 33, "end_offset": 37, "type": "<ALPHANUM>", "position": 7},
    {"token": "dogs", "start_offset": 38, "end_offset": 42, "type": "<ALPHANUM>", "position": 8}
  ]
}
```

💡 **Observation**:
- Text **lowercase me convert ho gaya** (e.g., `"Quick"` → `"quick"`).
- **Special characters jaise `!` remove ho gaye**.
- **Number (`3`) ka token type `<NUM>` hai**, baaki words ka `<ALPHANUM>` hai.  
- **Start & end offsets** batate hain ki word kaha mila text me.  

---  

## 🏗️ **3. Understanding the Standard Analyzer**  
**"standard" analyzer** Elasticsearch ka **default analyzer** hai. Yeh **3 main components** se milkar banta hai:  

1. **Character Filters:**  
   - Yeh **input text pe pre-processing karte hain** (e.g., HTML entity decoding).  
   - Standard analyzer me **koi character filter nahi hota**.  

2. **Tokenizer (Standard Tokenizer):**  
   - **Words ko alag-alag tokens me todta hai**.  
   - White spaces, punctuation, aur special characters ignore karta hai.  
   
3. **Token Filters:**  
   - **Lowercase filter** (uppercase ko lowercase me convert karta hai).  
   - **Stopword removal** (but standard analyzer default me stopwords nahi hataata).  

---  

## 🔄 **4. Tokenization Process**  
Agar tum yeh query run karo:  

```json
POST _analyze
{
  "text": "ElasticSearch is AMAZING!!!"
}
```

**Output**:
```json
{
  "tokens": [
    {"token": "elasticsearch", "start_offset": 0, "end_offset": 12, "type": "<ALPHANUM>", "position": 0},
    {"token": "is", "start_offset": 13, "end_offset": 15, "type": "<ALPHANUM>", "position": 1},
    {"token": "amazing", "start_offset": 16, "end_offset": 24, "type": "<ALPHANUM>", "position": 2}
  ]
}
```

💡 **Key Observations**:
- **Uppercase letters lowercase me convert ho gaye** (`"AMAZING"` → `"amazing"`).  
- **Punctuation (`!!!`) remove ho gaya**.  
- **Tokens ka type `<ALPHANUM>` hai**.  

---  

## 🏷️ **5. Metadata in Analyze API Response**  
Har token ke saath **extra metadata hota hai**, jo tumhe analysis process ko aur deeply samajhne me help karta hai:  

1. **`token`** → Actual tokenized word.  
2. **`start_offset` & `end_offset`** → Original text me token ki position.  
3. **`type`** → Token ka category (`<ALPHANUM>`, `<NUM>`, etc.).  
4. **`position`** → Token sequence order.  

---  

## 🧑‍🔬 **6. Custom Analyzers Breakdown**  
Agar tum **standard analyzer ke equivalent ek custom analyzer create karna chaho**, to aise hoga:  

```json
POST _analyze
{
  "char_filter": [],
  "tokenizer": "standard",
  "filter": ["lowercase"]
}
```

💡 **Yehi process tum kisi bhi custom analyzer ko test karne ke liye use kar sakte ho!**  

---  

## 📝 **7. Example Queries & Expected Output**  

### **🧐 Testing with Stopwords**
```json
POST _analyze
{
  "analyzer": "standard",
  "text": "This is an Elasticsearch tutorial"
}
```
**Output**:
```json
{
  "tokens": [
    {"token": "this", "position": 0},
    {"token": "is", "position": 1},
    {"token": "an", "position": 2},
    {"token": "elasticsearch", "position": 3},
    {"token": "tutorial", "position": 4}
  ]
}
```
🚨 **Standard analyzer stopwords remove nahi karta** (e.g., `"is"`, `"an"` bhi include hain).  

---

## 🎯 **8. Key Takeaways**  
✅ **Analyze API debugging ke liye bahut powerful tool hai**  
✅ **Standard analyzer tokens ko lowercase me convert karta hai aur punctuation remove karta hai**  
✅ **Metadata jaise offsets aur token type analysis ko aur insightful banate hain**  
✅ **Analyze API ka use custom analyzers banane aur test karne ke liye hota hai**  

