### **📌 Beats in Elastic Stack – Filebeat & Metricbeat** 🚀  

Beats **lightweight data shippers** hote hain jo different types ka data collect karke **Elasticsearch ya Logstash** me bhejte hain.  

Beats me **bahut saare modules** hain, but sabse zyada **Filebeat aur Metricbeat** use hote hain. Chalo inko **detail me samajhte hain**! 🔥  

---

## **1️⃣ Filebeat – Log Files Collector** 📂  
🔹 **Filebeat ek agent hai jo log files ka data collect karta hai aur Elastic Stack me bhejta hai.**  
🔹 Yeh **system logs, application logs, database logs, web server logs** sab kuch track kar sakta hai.  
🔹 Yeh **continuously logs read karta hai aur changes detect karta hai**, jisse koi bhi data loss na ho.  

### ✅ **Filebeat Ka Use Kaha Hota Hai?**  
✅ **Web Servers Logs** (e.g., Apache, Nginx)  
✅ **Application Logs** (e.g., Node.js, Java)  
✅ **Database Logs** (e.g., MySQL, MongoDB)  
✅ **Security Logs** (e.g., Firewall, VPN)  

### **🔥 Filebeat Ka Workflow**  
1️⃣ Filebeat **log files monitor** karega.  
2️⃣ Jab **naya log entry aayega**, Filebeat usko collect karega.  
3️⃣ Data ko **Elasticsearch ya Logstash** me bhejega.  
4️⃣ tum Kibana me logs ka **real-time analysis** kar sakte hao.  

#### **🛠️ Example (Apache Logs Monitor Karna)**  
```yaml
filebeat.inputs:
  - type: log
    paths:
      - "/var/log/apache2/*.log"
output.elasticsearch:
  hosts: ["http://localhost:9200"]
```
➡️ Yeh config **Apache logs ko Elasticsearch me bhejega**! 🚀  

---

## **2️⃣ Metricbeat – System & Application Metrics Collector** 📊  
🔹 **Metricbeat ek monitoring agent hai jo system aur application ka performance data collect karta hai.**  
🔹 Yeh **CPU, RAM, Disk Usage, Network Traffic, Database Queries** jaise metrics track karta hai.  
🔹 **Real-time monitoring aur alerting** ke liye best tool hai!  

### ✅ **Metricbeat Ka Use Kaha Hota Hai?**  
✅ **Server Monitoring** (CPU, Memory, Disk Usage)  
✅ **Database Monitoring** (MySQL, PostgreSQL, MongoDB)  
✅ **Docker & Kubernetes Monitoring**  
✅ **Cloud Services Monitoring** (AWS, Azure, GCP)  

### **🔥 Metricbeat Ka Workflow**  
1️⃣ Metricbeat system se **performance metrics collect karega.**  
2️⃣ Data ko **Elasticsearch ya Logstash me bhejega.**  
3️⃣ tum Kibana ke dashboard me **real-time server health dekh sakte hao.**  

#### **🛠️ Example (Server CPU & RAM Usage Monitor Karna)**  
```yaml
metricbeat.modules:
  - module: system
    metricsets:
      - cpu
      - memory
      - diskio
      - network
    period: 10s
output.elasticsearch:
  hosts: ["http://localhost:9200"]
```
➡️ Yeh config **server ke CPU, RAM, Disk usage ka data Elasticsearch me bhejega!** 🚀  

---

## **🎯 Filebeat vs Metricbeat – Kya Difference Hai?**  
| Feature       | Filebeat 📂 | Metricbeat 📊 |
|--------------|------------|--------------|
| **Purpose** | Logs collect karta hai | System & App Metrics collect karta hai |
| **Data Type** | Log files (text-based) | CPU, RAM, Disk, Network stats |
| **Use Case** | Log analysis & troubleshooting | Performance monitoring & alerts |
| **Example** | Web server logs, application logs | CPU usage, database queries |

---

## **🎯 Conclusion**  
✅ **Filebeat logs ka data bhejta hai, Metricbeat system aur application ka performance monitor karta hai.**  
✅ **Dono Elasticsearch ya Logstash ke saath kaam karte hain.**  
✅ **Agar tum logs analyze karna chahte ho, toh Filebeat best hai. Agar tum system monitoring chahte ho, toh Metricbeat best hai!** 🔥  