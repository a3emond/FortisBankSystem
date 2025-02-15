# JSP UI Frameworks & Design Guide

## Overview
This guide covers best practices for designing **user-friendly JSP interfaces** using **UI frameworks**, **templates**, and **responsive design** techniques for the Fortis Bank System.

---

## 1. Using Bootstrap with JSP
### **1.1 Adding Bootstrap to JSP**
Include Bootstrap via CDN in your `head` section:
```jsp
<head>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
```

### **1.2 Example Bootstrap Layout**
```jsp
<div class="container">
    <div class="row">
        <div class="col-md-6">
            <h2>Account Overview</h2>
        </div>
        <div class="col-md-6 text-end">
            <button class="btn btn-primary">New Transaction</button>
        </div>
    </div>
</div>
```

---

## 2. Implementing JSTL for Templating
**JavaServer Pages Standard Tag Library (JSTL)** simplifies JSP development.

### **2.1 Import JSTL**
Add JSTL to `pom.xml` if using Maven:
```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
```

### **2.2 Using JSTL for Dynamic Content**
```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<ul>
    <c:forEach var="account" items="${accounts}">
        <li>${account.name} - ${account.balance}</li>
    </c:forEach>
</ul>
```

---

## 3. Creating a Reusable JSP Template
**Separate common elements (header, footer, navbar) for reuse.**

### **3.1 Creating `header.jsp`**
```jsp
<header>
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
        <a class="navbar-brand" href="#">Fortis Bank</a>
    </nav>
</header>
```

### **3.2 Using `header.jsp` in Other Pages**
```jsp
<%@ include file="header.jsp" %>
```

---

## 4. Adding Form Validation
### **4.1 Frontend Validation with Bootstrap**
```jsp
<form>
    <div class="mb-3">
        <label for="email" class="form-label">Email address</label>
        <input type="email" class="form-control" required>
    </div>
    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

### **4.2 Backend Validation in Servlet**
```java
if (email == null || !email.matches("^[a-zA-Z0-9+_.-]+@[a-zA-Z0-9.-]+$")) {
    request.setAttribute("error", "Invalid email format");
    request.getRequestDispatcher("register.jsp").forward(request, response);
}
```

---

## 5. Responsive Design Best Practices
- Use **Bootstrap Grid System** for layouts.
- **Avoid fixed widths**; use `col-md-*`, `col-lg-*` classes.
- Test on multiple screen sizes using browser DevTools.

---

## 6. Optimizing JSP Performance
### **6.1 Reduce Page Load Time**
- **Minify CSS & JavaScript** files.
- **Use caching** for static resources.
- **Compress images** using modern formats (WebP).

### **6.2 Enable GZIP Compression in Tomcat**
Edit `server.xml` inside Tomcat:
```xml
<Connector port="8080" protocol="HTTP/1.1"
    compression="on"
    compressionMinSize="2048"
    noCompressionUserAgents="gozilla, traviata">
</Connector>
```

---

