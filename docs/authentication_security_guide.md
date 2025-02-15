# Authentication & Security Guide

## Overview
This guide provides a structured approach to securing the **Fortis Bank System** with authentication and security best practices. It includes user authentication via **OAuth 2.0, JWT tokens, session management**, and strategies to **protect against common vulnerabilities** like SQL Injection, CSRF, and XSS.

---

## 1. Authentication Methods
### **1.1 Using OAuth 2.0 and JWT Tokens**
**OAuth 2.0** is the preferred authentication method. JSON Web Tokens (**JWT**) will be used to handle user sessions securely.

#### **JWT Authentication Flow**
1. **User logs in** → Sends username & password.
2. **Server validates credentials** → Generates JWT token.
3. **Client stores token** → Sends token in `Authorization` header for subsequent requests.
4. **Server verifies JWT** → Decodes and authenticates request.

#### **Generating JWT Tokens (Spring Security Example)**
```java
public String generateToken(String username) {
    return Jwts.builder()
        .setSubject(username)
        .setIssuedAt(new Date())
        .setExpiration(new Date(System.currentTimeMillis() + 86400000))
        .signWith(SignatureAlgorithm.HS512, SECRET_KEY)
        .compact();
}
```

#### **Validating JWT Tokens**
```java
public Claims parseToken(String token) {
    return Jwts.parser()
        .setSigningKey(SECRET_KEY)
        .parseClaimsJws(token)
        .getBody();
}
```

---

## 2. Role-Based Access Control (RBAC)
### **2.1 User Roles**
Define different access levels for users:
- **Admin** → Manage all users and transactions.
- **Financial Advisor** → Manage client portfolios.
- **Teller Clerk** → Perform routine transactions.
- **Customer** → View balances and perform transactions.

### **2.2 Implementing RBAC with Spring Security**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .antMatchers("/admin/**").hasRole("ADMIN")
            .antMatchers("/advisor/**").hasRole("ADVISOR")
            .antMatchers("/customer/**").hasRole("CUSTOMER")
            .and().formLogin();
    }
}
```

---

## 3. Protecting Against Common Vulnerabilities
### **3.1 SQL Injection Prevention**
Always use **prepared statements** to avoid SQL injection:
```java
PreparedStatement stmt = connection.prepareStatement("SELECT * FROM users WHERE username = ?");
stmt.setString(1, username);
```

### **3.2 Cross-Site Scripting (XSS) Protection**
Sanitize user inputs to prevent XSS attacks:
```html
<input type="text" value="${fn:escapeXml(userInput)}">
```

### **3.3 Cross-Site Request Forgery (CSRF) Prevention**
Enable CSRF protection in Spring Security:
```java
http.csrf().csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse());
```

---

## 4. Multi-Factor Authentication (MFA)
MFA enhances security by requiring additional verification steps.
- **Step 1**: User enters username & password.
- **Step 2**: System sends OTP via email/SMS.
- **Step 3**: User enters OTP for verification.

### **4.1 Implementing MFA with Google Authenticator**
```java
TotpGenerator totp = new TotpGenerator("user-secret-key");
boolean isValid = totp.verify(otpCode);
```

---

