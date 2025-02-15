# Logging & Monitoring Guide

## Overview
This guide provides best practices for implementing **logging and monitoring** in the **Fortis Bank System**. It covers setting up logging frameworks, defining log levels, and integrating monitoring tools like **ELK (Elasticsearch, Logstash, Kibana)** and **Tomcat log management**.

---

## 1. Setting Up Logging
### **1.1 Choosing a Logging Framework**
- **Logback** (Recommended for Spring Boot)
- **SLF4J** (Simple Logging Facade for Java, bridges Logback & Log4j)
- **Java Util Logging (JUL)** (Default Java logging mechanism)

### **1.2 Configuring Logback for Logging**
Create `logback.xml` inside `src/main/resources`:
```xml
<configuration>
    <appender name="FILE" class="ch.qos.logback.core.FileAppender">
        <file>logs/application.log</file>
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} %-5level [%thread] %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>
    <root level="info">
        <appender-ref ref="FILE" />
    </root>
</configuration>
```

### **1.3 Using SLF4J in Java Classes**
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class AccountService {
    private static final Logger logger = LoggerFactory.getLogger(AccountService.class);
    
    public void createAccount(String user) {
        logger.info("Creating account for user: {}", user);
    }
}
```

---

## 2. Understanding Logging Levels
### **2.1 Common Logging Levels**
| Level  | Description                                  |
|--------|----------------------------------------------|
| DEBUG  | Detailed diagnostic messages                |
| INFO   | General application flow                    |
| WARN   | Potential issues, not critical errors       |
| ERROR  | System failures requiring attention         |
| FATAL  | Severe issues that cause application crash  |

### **2.2 Handling Exceptions Properly**
```java
try {
    // Code that may throw an exception
} catch (Exception e) {
    logger.error("Error occurred: ", e);
}
```

---

## 3. Writing Logs to Files or Cloud
### **3.1 Writing Logs to a File**
Logback automatically writes logs to `logs/application.log`. Ensure **log rotation** is configured to prevent large log files:
```xml
<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
    <fileNamePattern>logs/app-%d{yyyy-MM-dd}.log</fileNamePattern>
    <maxHistory>30</maxHistory>
</rollingPolicy>
```

### **3.2 Sending Logs to Cloud (ELK Stack)**
To send logs to **Elasticsearch**, configure **Logstash** to collect logs from files:
```json
input {
  file {
    path => "/var/log/app.log"
    start_position => "beginning"
  }
}
output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
  }
}
```

---

## 4. Monitoring Logs with ELK Stack (Elasticsearch, Logstash, Kibana)

### **4.1 Setting Up Elasticsearch**
```bash
docker run -d --name elasticsearch -p 9200:9200 -e "discovery.type=single-node" elasticsearch:7.10.1
```

### **4.2 Setting Up Logstash**
```bash
docker run -d --name logstash -p 5044:5044 -v /path/to/logstash.conf:/usr/share/logstash/pipeline/logstash.conf logstash:7.10.1
```

### **4.3 Setting Up Kibana Dashboard**
```bash
docker run -d --name kibana -p 5601:5601 kibana:7.10.1
```
Access the **Kibana Dashboard** at:
```
http://localhost:5601
```

---

## 5. Tomcat Log Management & Debugging
### **5.1 Accessing Tomcat Logs**
- **Catalina Log (Main Server Log)**:
  ```bash
  tail -f /usr/local/tomcat/logs/catalina.out
  ```
- **Application Logs** (if configured):
  ```bash
  tail -f /var/log/app.log
  ```

### **5.2 Configuring Tomcat Logging**
Modify `conf/logging.properties` inside Tomcat:
```properties
handlers = java.util.logging.FileHandler, java.util.logging.ConsoleHandler
java.util.logging.FileHandler.pattern = logs/tomcat.log
```

---

## 6. Setting Up Alerts & Notifications
Use **Prometheus + Grafana** for real-time monitoring & alerting.

### **6.1 Setting Up Prometheus**
```yaml
scrape_configs:
  - job_name: 'spring-boot-app'
    static_configs:
      - targets: ['localhost:8080']
```

### **6.2 Configuring Alerts in Grafana**
1. **Add Prometheus as a data source.**
2. **Create alert rules for ERROR spikes.**
3. **Send notifications via email or Slack.**

---

