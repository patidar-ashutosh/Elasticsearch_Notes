# **Stemming & Stop Words**

## **📌 Table of Contents**  
1️⃣ [Introduction](#1)  
2️⃣ [Stemming Kya Hota Hai?](#2)  
   - 🔹 [Example of Stemming](#2-1)  
   - 🔹 [Types of Stemming](#2-2)  

3️⃣ [Stop Words Kya Hote Hain?](#3)  
   - 🔹 [Example of Stop Words](#3-1)  
   - 🔹 [Stop Words Ko Remove Karne Ke Pros & Cons](#3-2)  
   
4️⃣ [Stemming & Stop Words Elasticsearch Me Kaise Kaam Karte Hain?](#4)  
5️⃣ [Conclusion](#5)  

---

## **1🔹 Introduction** <a id="1"></a>
Jab bhi hum **text analysis** ya **search engines** ki baat karte hain, toh **stemming** aur **stop words** do important concepts hote hain. Yeh dono hi search results ko improve karne me madad karte hain.

**Stemming** ka kaam words ko unke root (basic) form me reduce karna hota hai, aur **stop words** aise words hote hain jo search relevance ke liye important nahi hote, isliye unko ignore kiya jata hai.

Aao in dono concepts ko detail me samajhte hain. 🚀

---

## **2🔹 Stemming Kya Hota Hai?** <a id="2"></a>
Stemming ek process hai jisme **words ko unke root form me convert kiya jata hai**, taki search queries aur documents ke beech better matching ho sake.

### **🔸 Example of Stemming** <a id="2-1"></a>
| Original Word  | Stemmed Word |
|---------------|-------------|
| Loved        | Love        |
| Drinking     | Drink       |
| Bottles      | Bottle      |
| Running      | Run         |

Jaise ki agar koi user "loves" search kare aur document me "loved" likha ho, toh **without stemming**, search result nahi milega kyunki "loves" aur "loved" dono alag words hain. **Stemming is problem ko solve karta hai!**

### **🔸 Types of Stemming** <a id="2-2"></a>
1. **Porter Stemmer**: Simple aur fast algorithm jo common words ko stem karta hai. (e.g., "running" → "run")
2. **Lancaster Stemmer**: Thoda aggressive algorithm hai jo words ko aur chhota bana deta hai. (e.g., "running" → "ru")
3. **Snowball Stemmer**: Porter Stemmer ka improved version jo multiple languages ko support karta hai.

Elasticsearch me mostly **Porter Stemmer** ya **Snowball Stemmer** use hota hai.

---

## **3🔹 Stop Words Kya Hote Hain?** <a id="3"></a>
**Stop words** wo common words hote hain jo search results me relevance increase nahi karte, isliye unko **ignore** kar diya jata hai.

### **🔸 Example of Stop Words** <a id="3-1"></a>
| Sentence | Stop Words Remove Karne Ke Baad |
|----------|--------------------------------|
| "The bottle is on the table" | "bottle table" |
| "He is running in the park"  | "running park" |

### **🔸 Stop Words Ko Remove Karne Ke Pros & Cons** <a id="3-2"></a>
✅ **Advantages**
- Index size kam hota hai (Storage bachti hai)
- Search speed fast hoti hai
- Irrelevant words hatne se better ranking hoti hai

❌ **Disadvantages**
- Kabhi-kabhi search meaning change ho sakta hai (e.g., "To be or not to be")
- Advanced NLP techniques me stop words useful hote hain

Elasticsearch me **by default stop words remove nahi kiye jate**, kyunki naye search algorithms unhe intelligently handle kar sakte hain.

---

## **4🔹 Stemming & Stop Words Elasticsearch Me Kaise Kaam Karte Hain?** <a id="4"></a>
- Elasticsearch me **analyzers** ka use hota hai jo **stemming aur stop words** ko handle karte hain.
- **Default analyzer (Standard Analyzer)**: Lowercasing karta hai aur punctuation remove karta hai, lekin stemming ya stop words remove nahi karta.
- **Custom analyzers** bana ke hum stemming aur stop words ko enable/disable kar sakte hain.

#### **Example: Standard Analyzer Ka Output**
_Input:_ "Drinking bottles are on the table."
_Output:_ `[drinking, bottles, are, on, the, table]` (Stop words remove nahi hue)

#### **Example: Custom Analyzer (With Stemming & Stop Words Removal)**
_Input:_ "Drinking bottles are on the table."
_Output:_ `[drink, bottle, table]` (Stop words hat gaye aur stemming apply ho gaya)

---

## **5🔹 Conclusion** <a id="5"></a>
- **Stemming** words ko unke root form me convert karta hai taki better search results milein.
- **Stop words** wo common words hote hain jo search relevance ke liye important nahi hote, isliye unhe remove kiya jata hai.
- **Elasticsearch me default analyzer stop words ko remove nahi karta, lekin stemming apply kar sakte hain.**
- **Best practice** yeh hai ki **stop words tabhi remove karein jab required ho**, aur stemming carefully use karein taki meaning distort na ho.

