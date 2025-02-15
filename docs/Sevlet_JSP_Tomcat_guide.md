# Guide to Using Servlets, JSP, and Tomcat

## Overview
This guide covers how to develop, deploy, and run **Servlets and JSP** applications using **Apache Tomcat**. It includes project setup, coding examples, deployment, and best practices.

---

## Prerequisites
Ensure you have the following installed:
- **Java Development Kit (JDK 11+)**
- **Apache Tomcat (via Docker or standalone installation)**
- **Maven (for dependency management)**
- **An IDE (Eclipse, IntelliJ IDEA, or VS Code with Java support)**

---

## Project Structure
A typical **Servlet-JSP** project follows this structure:
```
MyWebApp/
 ├── src/
 │   ├── main/
 │   │   ├── java/com/example/servlets/
 │   │   │   ├── HelloServlet.java
 │   │   ├── webapp/
 │   │   │   ├── index.jsp
 │   │   │   ├── WEB-INF/
 │   │   │   │   ├── web.xml
 ├── pom.xml (if using Maven)
```

---

## Creating a Servlet
### **1. Define the Servlet**
```java
package com.example.servlets;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        response.getWriter().println("<h1>Hello, Servlet!</h1>");
    }
}
```

### **2. Configure `web.xml` (if not using annotations)**
```xml
<web-app xmlns="http://java.sun.com/xml/ns/javaee" version="3.0">
    <servlet>
        <servlet-name>HelloServlet</servlet-name>
        <servlet-class>com.example.servlets.HelloServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>HelloServlet</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

---

## Creating a JSP Page
Create **index.jsp** inside the `webapp/` directory:
```jsp
<%@ page contentType="text/html; charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>JSP Example</title>
</head>
<body>
    <h1>Welcome to JSP!</h1>
    <p>Current time: <%= new java.util.Date() %></p>
</body>
</html>
```

---

## Deploying to Tomcat

### **1. Package the Application (WAR File)**
If using Maven, package the project into a WAR file:
```bash
mvn clean package
```
The WAR file will be located in `target/MyWebApp.war`.

### **2. Deploy WAR to Tomcat**
Copy the WAR file into Tomcat's `webapps/` directory:
```bash
cp target/MyWebApp.war /usr/local/tomcat/webapps/
```

For **Docker-based Tomcat**, copy the WAR into the `./webapps` folder and restart the container:
```bash
docker-compose restart tomcat-server
```

### **3. Access the Application**
Once deployed, access the Servlet and JSP via:
```
http://localhost:8080/MyWebApp/index.jsp
http://localhost:8080/MyWebApp/hello
```

---

## Tomcat Configuration
### **Enabling Manager GUI**
Ensure `conf/tomcat-users.xml` contains:
```xml
<tomcat-users>
    <user username="admin" password="admin" roles="manager-gui,admin-gui"/>
</tomcat-users>
```

### **Managing Applications via Tomcat Manager**
Navigate to:
```
http://localhost:8080/manager/html
```
Login with `admin/admin` (as configured above) to deploy and manage applications.

---

## Common Errors & Solutions
### **1. 404 Error - Servlet Not Found**
- Ensure `web.xml` or `@WebServlet` mapping is correct.
- Restart Tomcat after deploying.

### **2. 500 Internal Server Error**
- Check logs under `logs/catalina.out` (Linux/macOS) or `logs/catalina.log` (Windows).

### **3. JSP Changes Not Reflecting**
- Disable JSP caching by adding to `web.xml`:
```xml
<context-param>
    <param-name>org.apache.jasper.compiler.disablejsr199</param-name>
    <param-value>true</param-value>
</context-param>
```

---

## Best Practices
1. **Use MVC Pattern**: Separate Servlets (controllers) from JSP (views).
2. **Avoid Business Logic in JSP**: Keep JSP for presentation only.
3. **Use Session Management**: Track user sessions securely with:
   ```java
   HttpSession session = request.getSession();
   session.setAttribute("username", "JohnDoe");
   ```
4. **Enable HTTPS**: Secure web applications using SSL/TLS.
5. **Log Errors Properly**: Use `java.util.logging` or a logging framework.

---
