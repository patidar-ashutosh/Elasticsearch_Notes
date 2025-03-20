# Built-in Analyzers in Elasticsearch

## Table of Contents

1. [Introduction](#introduction)
2. [What Are Built-in Analyzers?](#what-are-built-in-analyzers)
3. [Types of Built-in Analyzers](#types-of-built-in-analyzers)
   - [Standard Analyzer](#standard-analyzer)
   - [Simple Analyzer](#simple-analyzer)
   - [Whitespace Analyzer](#whitespace-analyzer)
   - [Keyword Analyzer](#keyword-analyzer)
   - [Pattern Analyzer](#pattern-analyzer)
   - [Language-Specific Analyzers](#language-specific-analyzers)
4. [Using Built-in Analyzers in Field Mappings](#using-built-in-analyzers-in-field-mappings)
5. [Configuring Built-in Analyzers](#configuring-built-in-analyzers)
6. [Conclusion](#conclusion)

---

## 1. Introduction

Elasticsearch me **analyzers** ka use text ko process karne ke liye hota hai, taki search queries aur indexed documents ka format match ho sake. Built-in analyzers pre-configured hote hain, jisme **character filters, tokenizers aur token filters** ka combination hota hai.

## 2. What Are Built-in Analyzers?

Built-in analyzers Elasticsearch ke andar **predefined text processing tools** hain. Yeh predefined hote hain taki text ko efficiently analyze kiya ja sake. Tum custom analyzers bhi bana sakte ho, lekin built-in analyzers time aur effort bachane ke liye useful hote hain.

## 3. Types of Built-in Analyzers

Elasticsearch me kai tareeke ke built-in analyzers hote hain jo alag-alag use cases ke liye kaam aate hain. Chalo inhe detail me dekhte hain:

### 3.1 Standard Analyzer

- Default analyzer jo Elasticsearch use karta hai.
- Text ko words me split karta hai (word boundaries ke basis pe).
- Punctuation remove karta hai.
- Lowercase conversion karta hai.
- Stop words ka filter **by default disabled** rehta hai, lekin enable kiya ja sakta hai.

**Example:**

Input: `Hello, this is an Elasticsearch example!`

Output Tokens: `[hello, this, is, an, elasticsearch, example]`

### 3.2 Simple Analyzer

- Standard analyzer jaisa hi hota hai, lekin words ko **non-letter characters** pe split karta hai.
- Lowercase conversion karta hai.

**Example:**

Input: `Hello123World`

Output Tokens: `[hello, world]`

### 3.3 Whitespace Analyzer

- Text ko **whitespace** ke basis pe split karta hai.
- Punctuation remove nahi karta aur lowercase conversion bhi nahi karta.

**Example:**

Input: `HELLO World! Welcome_to Elasticsearch`

Output Tokens: `[HELLO, World!, Welcome_to, Elasticsearch]`

### 3.4 Keyword Analyzer

- Text ko **as-is** rakhta hai, koi changes nahi karta.
- Yeh mostly **exact match** queries ke liye use hota hai.

**Example:**

Input: `Hello World!`

Output Token: `[Hello World!]`

### 3.5 Pattern Analyzer

- Yeh **custom regex pattern** ka use karta hai tokenization ke liye.
- Default pattern **non-word characters** ko tokenize karta hai.

**Example:**

Input: `2024-03-04, User-Name`

Regex Pattern: `[-, ]+`

Output Tokens: `[2024, 03, 04, User, Name]`

### 3.6 Language-Specific Analyzers

- Yeh **particular language ke rules** follow karte hain.
- Example: `english`, `french`, `german`, etc.
- English analyzer ke andar **stemming, lowercase conversion, stop words removal** hoti hai.

**Example:**

Input: `Peter's dog is running happily.`

Output Tokens: `[peter, dog, run, happili]`

## 4. Using Built-in Analyzers in Field Mappings

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

## 5. Configuring Built-in Analyzers

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

## 6. Conclusion

- **Built-in analyzers** text processing ko easy banate hain.
- Different analyzers different use cases ke liye hote hain.
- Field mappings me analyzers ko specify kiya ja sakta hai.
- Configuration options available hain jo analyzers ko customize karne me madad karte hain.