# Frontend-Backend Communication Guide

## Overview
This guide covers best practices for handling **frontend-backend communication** in the **Fortis Bank System**. It includes making **AJAX calls from JSP**, using **Fetch API vs. Axios**, handling responses, integrating **WebSockets for real-time updates**, and configuring **CORS**.

---

## 1. Making AJAX Calls from JSP Using JavaScript
AJAX allows JSP pages to fetch data from the backend **without refreshing the page**.

### **1.1 Using Fetch API**
```html
<script>
  async function fetchAccountDetails(accountId) {
    const response = await fetch(`https://api.fortisbank.com/v1/accounts/${accountId}`, {
      method: 'GET',
      headers: {
        'Authorization': 'Bearer YOUR_ACCESS_TOKEN',
        'Content-Type': 'application/json'
      }
    });
    
    if (!response.ok) {
      console.error('Failed to fetch account details');
      return;
    }
    
    const data = await response.json();
    console.log(data);
  }
  
  fetchAccountDetails(123);
</script>
```

### **1.2 Using jQuery AJAX in JSP**
```jsp
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script>
  $(document).ready(function() {
    $.ajax({
      url: "https://api.fortisbank.com/v1/accounts/123",
      method: "GET",
      headers: {
        "Authorization": "Bearer YOUR_ACCESS_TOKEN"
      },
      success: function(response) {
        console.log("Account Data:", response);
      },
      error: function(xhr, status, error) {
        console.error("Error fetching account details:", error);
      }
    });
  });
</script>
```

---

## 2. Fetch API vs. Axios for API Calls

| Feature | Fetch API | Axios |
|---------|----------|-------|
| Syntax | Native | Requires installation |
| Error Handling | Needs `response.ok` check | Auto-rejects non-2xx responses |
| JSON Parsing | `response.json()` needed | Auto-parses JSON |
| Request Cancellation | Not supported | Supports aborting requests |
| Interceptors | Not built-in | Supports request/response interceptors |

### **2.1 Using Axios for API Calls**
```html
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
<script>
  axios.get('https://api.fortisbank.com/v1/accounts/123', {
    headers: { 'Authorization': 'Bearer YOUR_ACCESS_TOKEN' }
  })
  .then(response => console.log(response.data))
  .catch(error => console.error('Error fetching data:', error));
</script>
```

---

## 3. Handling API Responses & Errors Properly

### **3.1 Handling Errors in Fetch API**
```javascript
async function fetchData(url) {
    try {
        let response = await fetch(url, { method: 'GET' });
        if (!response.ok) {
            throw new Error(`HTTP error! Status: ${response.status}`);
        }
        return await response.json();
    } catch (error) {
        console.error('Error fetching data:', error);
    }
}
```

### **3.2 Handling Errors in Axios**
```javascript
axios.get('https://api.fortisbank.com/v1/accounts/123')
  .then(response => console.log(response.data))
  .catch(error => {
    if (error.response) {
        console.error('Server responded with:', error.response.status);
    } else if (error.request) {
        console.error('No response received');
    } else {
        console.error('Axios error:', error.message);
    }
  });
```

---

## 4. Integrating WebSockets for Real-Time Updates
WebSockets enable **real-time updates** without polling the server.

### **4.1 Backend WebSocket Configuration (Spring Boot)**
```java
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {
    @Override
    public void registerStompEndpoints(StompEndpointRegistry registry) {
        registry.addEndpoint("/ws").setAllowedOrigins("*").withSockJS();
    }
}
```

### **4.2 Frontend WebSocket Connection**
```javascript
const socket = new SockJS("http://localhost:8080/ws");
const stompClient = Stomp.over(socket);
stompClient.connect({}, () => {
    console.log("Connected to WebSocket");
});
```

---

## 5. Cross-Origin Resource Sharing (CORS) Configuration
If the frontend runs on a different domain from the backend, **CORS** must be enabled.

### **5.1 Configuring CORS in Spring Boot Backend**
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class CorsConfig {
    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/**").allowedOrigins("http://localhost:8080");
            }
        };
    }
}
```

### **5.2 CORS Headers in Response**
Ensure the backend sends proper CORS headers:
```java
response.setHeader("Access-Control-Allow-Origin", "*");
response.setHeader("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS");
response.setHeader("Access-Control-Allow-Headers", "Content-Type, Authorization");
```

---
