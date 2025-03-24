# Built-in Analyzers in Elasticsearch

## **📌 Table of Contents**  
1️⃣ [Introduction](#1)  
2️⃣ [What Are Built-in Analyzers?](#2)  
3️⃣ [Types of Built-in Analyzers](#3)  
   - [Standard Analyzer](#3-1)  
   - [Simple Analyzer](#3-2)  
   - [Whitespace Analyzer](#3-3)  
   - [Keyword Analyzer](#3-4)  
   - [Pattern Analyzer](#3-5)  
   - [Language-Specific Analyzers](#3-6)  

4️⃣ [Using Built-in Analyzers in Field Mappings](#4)  
5️⃣ [Configuring Built-in Analyzers](#5)  
6️⃣ [Conclusion](#6)  

---

## 1. Introduction <a id="1"></a>

Elasticsearch me **analyzers** ka use text ko process karne ke liye hota hai, taki search queries aur indexed documents ka format match ho sake. Built-in analyzers pre-configured hote hain, jisme **character filters, tokenizers aur token filters** ka combination hota hai.

## 2. What Are Built-in Analyzers? <a id="2"></a>

Built-in analyzers Elasticsearch ke andar **predefined text processing tools** hain. Yeh predefined hote hain taki text ko efficiently analyze kiya ja sake. Tum custom analyzers bhi bana sakte ho, lekin built-in analyzers time aur effort bachane ke liye useful hote hain.

## 3. Types of Built-in Analyzers <a id="3"></a>

Elasticsearch me kai tareeke ke built-in analyzers hote hain jo alag-alag use cases ke liye kaam aate hain. Chalo inhe detail me dekhte hain:

### 3.1 Standard Analyzer <a id="3-1"></a>

- Default analyzer jo Elasticsearch use karta hai.
- Text ko words me split karta hai (word boundaries ke basis pe).
- Punctuation remove karta hai.
- Lowercase conversion karta hai.
- Stop words ka filter **by default disabled** rehta hai, lekin enable kiya ja sakta hai.

**Example:**

Input: `Hello, this is an Elasticsearch example!`

Output Tokens: `[hello, this, is, an, elasticsearch, example]`

### 3.2 Simple Analyzer <a id="3-2"></a>

- Standard analyzer jaisa hi hota hai, lekin words ko **non-letter characters** pe split karta hai.
- Lowercase conversion karta hai.

**Example:**

Input: `Hello123World`

Output Tokens: `[hello, world]`

### 3.3 Whitespace Analyzer <a id="3-3"></a>

- Text ko **whitespace** ke basis pe split karta hai.
- Punctuation remove nahi karta aur lowercase conversion bhi nahi karta.

**Example:**

Input: `HELLO World! Welcome_to Elasticsearch`

Output Tokens: `[HELLO, World!, Welcome_to, Elasticsearch]`

### 3.4 Keyword Analyzer <a id="3-4"></a>

- Text ko **as-is** rakhta hai, koi changes nahi karta.
- Yeh mostly **exact match** queries ke liye use hota hai.

**Example:**

Input: `Hello World!`

Output Token: `[Hello World!]`

### 3.5 Pattern Analyzer <a id="3-5"></a>

- Yeh **custom regex pattern** ka use karta hai tokenization ke liye.
- Default pattern **non-word characters** ko tokenize karta hai.

**Example:**

Input: `2024-03-04, User-Name`

Regex Pattern: `[-, ]+`

Output Tokens: `[2024, 03, 04, User, Name]`

### 3.6 Language-Specific Analyzers <a id="3-6"></a>

- Yeh **particular language ke rules** follow karte hain.
- Example: `english`, `french`, `german`, etc.
- English analyzer ke andar **stemming, lowercase conversion, stop words removal** hoti hai.

**Example:**

Input: `Peter's dog is running happily.`

Output Tokens: `[peter, dog, run, happili]`

## 4. Using Built-in Analyzers in Field Mappings <a id="4"></a>

Elasticsearch me built-in analyzers ko field mappings me directly specify kiya ja sakta hai.

**Example Mapping:**

```json
{
  "mappings": {
    "properties": {
      "description": {
        "type": "text",
        "analyzer": "english"
      }
    }
  }
}
```

Yeh mapping **description** field pe `english` analyzer apply karegi.

## 5. Configuring Built-in Analyzers <a id="5"></a>

Built-in analyzers ko customize bhi kiya ja sakta hai.

**Example: Standard Analyzer me Stop Words Enable Karna**

```json
{
  "settings": {
    "analysis": {
      "analyzer": {
        "remove_english_stop_words": {
          "type": "standard",
          "stopwords": "_english_"
        }
      }
    }
  }
}
```

Isme `standard` analyzer me English stop words remove karne ka option enable kiya gaya hai.

## 6. Conclusion <a id="6"></a>

- **Built-in analyzers** text processing ko easy banate hain.
- Different analyzers different use cases ke liye hote hain.
- Field mappings me analyzers ko specify kiya ja sakta hai.
- Configuration options available hain jo analyzers ko customize karne me madad karte hain.