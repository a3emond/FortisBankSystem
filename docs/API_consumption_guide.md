# API Consumption Guide

## Overview
This document provides a detailed guide on how to consume the Fortis Bank System API efficiently, ensuring smooth integration with both web-based and desktop applications. It covers authentication, request handling, error management, and best practices.

---

## General API Usage Guidelines
### **Base URL**
All API requests should be prefixed with:
```
https://api.fortisbank.com/v1
```

### **Authentication & Security**
- The API requires authentication using **OAuth 2.0**.
- You must include a **Bearer Token** in the `Authorization` header for secured endpoints.
```http
Authorization: Bearer <token>
```
- Ensure that sensitive data is **encrypted in transit**.

---

## Consuming the API in Different Environments

### **1. Java (Spring Boot Web App)**
#### **Making API Calls using RestTemplate**
```java
RestTemplate restTemplate = new RestTemplate();
HttpHeaders headers = new HttpHeaders();
headers.set("Authorization", "Bearer YOUR_ACCESS_TOKEN");
HttpEntity<String> entity = new HttpEntity<>(headers);

ResponseEntity<String> response = restTemplate.exchange(
    "https://api.fortisbank.com/v1/accounts/123",
    HttpMethod.GET,
    entity,
    String.class
);
System.out.println(response.getBody());
```

#### **Making API Calls using WebClient**
```java
WebClient webClient = WebClient.builder()
    .baseUrl("https://api.fortisbank.com/v1")
    .defaultHeader(HttpHeaders.AUTHORIZATION, "Bearer YOUR_ACCESS_TOKEN")
    .build();

Account account = webClient.get()
    .uri("/accounts/123")
    .retrieve()
    .bodyToMono(Account.class)
    .block();
```

---

### **2. Java (JSP Desktop Application)**
#### **Consuming API using HttpURLConnection**
```java
URL url = new URL("https://api.fortisbank.com/v1/accounts/123");
HttpURLConnection conn = (HttpURLConnection) url.openConnection();
conn.setRequestMethod("GET");
conn.setRequestProperty("Authorization", "Bearer YOUR_ACCESS_TOKEN");

int responseCode = conn.getResponseCode();
if (responseCode == HttpURLConnection.HTTP_OK) {
    BufferedReader in = new BufferedReader(new InputStreamReader(conn.getInputStream()));
    String inputLine;
    StringBuffer response = new StringBuffer();
    while ((inputLine = in.readLine()) != null) {
        response.append(inputLine);
    }
    in.close();
    System.out.println(response.toString());
}
```

---

## Common API Requests

### **1. Fetch Account Details**
#### **Request:**
```http
GET /accounts/123
Authorization: Bearer YOUR_ACCESS_TOKEN
```
#### **Response:**
```json
{
    "status": "success",
    "data": {
        "accountId": 123,
        "accountType": "savings",
        "balance": 2500.75
    }
}
```

### **2. Create a New Transaction**
#### **Request:**
```http
POST /transactions
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
    "fromAccount": 123,
    "toAccount": 789,
    "amount": 150.00
}
```
#### **Response:**
```json
{
    "status": "success",
    "transactionId": 98765,
    "message": "Transaction successful"
}
```

---

## Error Handling
### **Response Structure for Errors**
```json
{
    "status": "error",
    "message": "Invalid credentials"
}
```

### **Common Error Codes**
- **400 Bad Request** → Invalid request format
- **401 Unauthorized** → Missing or invalid authentication
- **403 Forbidden** → Insufficient permissions
- **404 Not Found** → Requested resource not found
- **500 Internal Server Error** → Server-side issue

---

## Best Practices for API Consumption
1. **Use Caching**: Reduce redundant API calls by caching frequently accessed data.
2. **Handle Errors Gracefully**: Implement proper error-handling mechanisms.
3. **Limit API Calls**: Adhere to rate limits to prevent being blocked.
4. **Secure API Keys**: Store authentication tokens securely and avoid hardcoding them.
5. **Use Async Processing**: Implement asynchronous requests where possible to prevent UI blocking.

---

