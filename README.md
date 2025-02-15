# Fortis Bank System

## Overview
The Fortis Bank System is a centralized banking application designed to modernize banking operations. This project follows modular development to ensure scalability and maintainability. It includes role-based access control, multi-account support, secure transactions, financial advising, and reporting functionalities.

## 📖 Documentation Structure
All guides and documentation files are located in the `docs/` folder. Below is the table of contents linking to each guide.

## 📖 Table of Contents
- [API Consumption Guide](docs/API_consumption_guide.md)
- [API Contracts Guide](docs/API_contracts_guide.md)
- [Authentication & Security Guide](docs/authentication_security_guide.md)
- [Database Communication (No API)](docs/DB_communication_guide_noAPI.md)
- [Oracle XE Database Connection](docs/DB_connection_guide_oracleXE.md)
- [Docker Compose Setup](docs/docker_compose_setup_guide.md)
- [Frontend-Backend Communication](docs/frontend-backend_communication_guide.md)
- [Java Project Structure & Flow](docs/Java_project_structure_flow_guide.md)
- [JSP UI Design Guide](docs/JSP_UI_design_guide.md)
- [Logging & Monitoring](docs/logging_monitoring_guide.md)
- [Naming Conventions & Best Practices](docs/naming_conventions_best_practices_guide.md)
- [Servlet & JSP Tomcat Guide](docs/Servlet_JSP_Tomcat_guide.md)

## Work Breakdown Structure (WBS)

### **Phase 1: System Analysis & Design**
✅ Define System Requirements  
✅ UML Diagrams (Use Case, Class, Sequence, Activity)  
✅ ERD & Database Schema Design  
✅ Define User Roles & Permissions  
✅ Set Up Repo with Conventions  

### **Phase 2: Backend Development (BLL & DAL Focus)**
#### **User Authentication & RBAC**
🔲 Implement user authentication via JWT/OAuth  
🔲 Define role-based access control (RBAC)  
🔲 Setup password encryption & secure login  
🔲 Implement Multi-Factor Authentication (MFA)  

#### **Bank Account Management Module**
🔲 Define account entity & relationships  
🔲 Implement account creation & deletion  
🔲 Implement account update & retrieval APIs  
🔲 Enforce business rules (account limits, validation)  

#### **Transaction Processing Module**
🔲 Implement deposit & withdrawal operations  
🔲 Implement internal & external transfers  
🔲 Enforce transaction validation rules  
🔲 Implement transaction history logging  

#### **Financial Advisor Appointment System**
🔲 Define appointment entity & scheduling logic  
🔲 Implement appointment booking & cancellation  
🔲 Set up advisor availability & scheduling rules  
🔲 Notify users of upcoming appointments  

#### **Teller Clerk Transaction Support**
🔲 Implement teller-specific role & permissions  
🔲 Provide teller interface for assisted transactions  
🔲 Implement cash management & reconciliation  

#### **Interest Calculation & Credit Limits**
🔲 Define interest rate rules per account type  
🔲 Implement scheduled interest calculations  
🔲 Enforce credit limit policies  
🔲 Generate periodic interest statements  

#### **Reports & Audits API**
🔲 Implement user activity logging  
🔲 Generate financial reports (monthly, yearly)  
🔲 Implement fraud detection & alerts  
🔲 Secure sensitive reporting data  

### **Phase 3: Integration & Security**
#### **API Endpoints Testing**
🔲 Define API contracts & expected responses  
🔲 Implement unit & integration testing  
🔲 Validate API security & input sanitization  

#### **MFA & Encryption**
🔲 Implement Multi-Factor Authentication (MFA)  
🔲 Encrypt sensitive user data (passwords, transactions)  
🔲 Secure database connections & access keys  

#### **Logging & Monitoring**
🔲 Set up system-wide logging (Logback, ELK)  
🔲 Implement API request & response logging  
🔲 Set up alerts for failed transactions  

#### **Notifications & Alerts**
🔲 Implement email/SMS notifications  
🔲 Define alert thresholds for suspicious activities  
🔲 Implement real-time user alerts for transactions  

### **Phase 4: Deployment & Documentation**
#### **CI/CD with GitHub Actions**
🔲 Set up automated builds & testing  
🔲 Implement deployment pipeline (Docker, Kubernetes)  
🔲 Ensure rollback mechanisms for failed deployments  

#### **Final Security Review**
🔲 Perform penetration testing & vulnerability scans  
🔲 Enforce security policies across all modules  
🔲 Ensure compliance with financial data regulations  

#### **Deployment to Cloud / Server**
🔲 Configure cloud hosting & database scaling  
🔲 Implement load balancing & failover strategies  
🔲 Optimize system for high availability  

#### **Full System Documentation**
🔲 Finalize API documentation (Swagger/OpenAPI)  
🔲 Generate user manuals & onboarding guides  
🔲 Create video tutorials for key workflows  

## Contribution Workflow
1. **Fork & Clone** the repository.
2. Create a **feature branch**: `git checkout -b feature/your-feature`
3. Make changes, commit using conventional commits.
4. Push to your branch: `git push origin feature/your-feature`
5. Submit a **Pull Request** for review.
6. Once approved, merge into `develop`.

## Issue Tracking
- Issues will be tracked using **GitHub Issues & Projects**.
- Each task will be categorized under appropriate milestones.

## Module Documentation Template
Each team member contributing a module must provide a documentation file following this template:

### **Module Name**
#### **Description**
Briefly describe what the module does and its role in the system.

#### **Setup Instructions**
- Dependencies required
- Configuration settings (if any)
- How to build and run the module

#### **Usage Guide**
- Key classes and methods
- Example API calls (if applicable)
- How other modules interact with this one

#### **Error Handling**
- Expected errors and how to handle them

#### **Future Improvements**
- Possible extensions or optimizations

---

# Fortis Bank System - Complete Project Structure & Method Breakdown

## 📂 Project Directory Structure

```
FortisBankSystem/
 ├── src/
 │   ├── main/
 │   │   ├── java/com/fortisbank/
 │   │   │   ├── controller/        # Handles HTTP requests
 │   │   │   │   ├── AccountController.java
 │   │   │   │   ├── TransactionController.java
 │   │   │   │   ├── UserController.java
 │   │   │   │   ├── AdvisorController.java
 │   │   │   ├── service/          # Business logic layer
 │   │   │   │   ├── AccountService.java
 │   │   │   │   ├── TransactionService.java
 │   │   │   │   ├── UserService.java
 │   │   │   │   ├── AdvisorService.java
 │   │   │   ├── repository/       # Database interactions
 │   │   │   │   ├── AccountRepository.java
 │   │   │   │   ├── TransactionRepository.java
 │   │   │   │   ├── UserRepository.java
 │   │   │   ├── model/            # Entities representing DB tables
 │   │   │   │   ├── Account.java
 │   │   │   │   ├── Transaction.java
 │   │   │   │   ├── User.java
 │   │   │   │   ├── Advisor.java
 │   │   │   ├── dto/              # Data Transfer Objects
 │   │   │   │   ├── AccountDTO.java
 │   │   │   │   ├── TransactionDTO.java
 │   │   │   │   ├── UserDTO.java
 │   │   │   ├── exception/        # Custom error handling
 │   │   │   │   ├── GlobalExceptionHandler.java
 │   │   │   │   ├── ResourceNotFoundException.java
 │   │   │   ├── config/           # Application configurations
 │   │   │   │   ├── SecurityConfig.java
 │   │   │   │   ├── CorsConfig.java
 │   │   │   ├── security/         # Authentication & Authorization
 │   │   │   │   ├── JwtUtil.java
 │   │   │   │   ├── UserDetailsServiceImpl.java
 │   │   │   ├── FortisBankApplication.java  # Main entry point
 │   │   ├── resources/
 │   │   │   ├── application.properties  # Config settings
 │   ├── test/  # Unit & integration tests
 ├── docs/  # Project documentation
 ├── docker-compose.yml  # Docker configuration
 ├── pom.xml  # Maven dependencies
 ├── README.md
```

---

## 📌 Breakdown of Key Components

### **1️⃣ Controller Layer** (`controller/`)
Handles API requests and delegates to services.

#### **AccountController.java**
```java
@GetMapping("/{id}")
public ResponseEntity<AccountDTO> getAccountById(@PathVariable Long id);

@PostMapping("/create")
public ResponseEntity<AccountDTO> createAccount(@RequestBody AccountDTO accountDTO);
```

#### **TransactionController.java**
```java
@PostMapping("/transfer")
public ResponseEntity<TransactionDTO> transferFunds(@RequestBody TransactionDTO transactionDTO);
```

#### **UserController.java**
```java
@GetMapping("/profile")
public ResponseEntity<UserDTO> getUserProfile(@RequestParam Long userId);
```

---

### **2️⃣ Service Layer** (`service/`)
Contains business logic and calls repository methods.

#### **AccountService.java**
```java
public AccountDTO getAccountById(Long id);
public AccountDTO createAccount(AccountDTO accountDTO);
```

#### **TransactionService.java**
```java
public TransactionDTO transferFunds(TransactionDTO transactionDTO);
```

#### **UserService.java**
```java
public UserDTO getUserProfile(Long userId);
```

---

### **3️⃣ Repository Layer** (`repository/`)
Handles direct database communication.

#### **AccountRepository.java**
```java
Optional<Account> findById(Long id);
Account save(Account account);
```

#### **TransactionRepository.java**
```java
Transaction save(Transaction transaction);
List<Transaction> findByAccountId(Long accountId);
```

#### **UserRepository.java**
```java
Optional<User> findByEmail(String email);
```

---

### **4️⃣ Model Layer (Entities)** (`model/`)
Represents database tables.

#### **Account.java**
```java
private Long id;
private String accountNumber;
private Double balance;
private User owner;
```

#### **Transaction.java**
```java
private Long id;
private Double amount;
private String type;
private Account sourceAccount;
private Account targetAccount;
```

#### **User.java**
```java
private Long id;
private String username;
private String email;
private String password;
private Role role;
```

---

### **5️⃣ DTO Layer (Data Transfer Objects)** (`dto/`)
Prevents direct entity exposure in APIs.

#### **AccountDTO.java**
```java
private String accountNumber;
private Double balance;
```

#### **TransactionDTO.java**
```java
private Double amount;
private String sourceAccount;
private String targetAccount;
```

#### **UserDTO.java**
```java
private String username;
private String email;
```

---

### **6️⃣ Security Layer** (`security/`)
Handles authentication and authorization.

#### **JwtUtil.java**
```java
public String generateToken(String username);
public boolean validateToken(String token, UserDetails userDetails);
```

#### **UserDetailsServiceImpl.java**
```java
public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
```

---

### **7️⃣ Configuration Layer** (`config/`)
Handles application-wide configurations.

#### **SecurityConfig.java**
```java
@Override
protected void configure(HttpSecurity http) throws Exception;
```

#### **CorsConfig.java**
```java
@Bean
public WebMvcConfigurer corsConfigurer();
```

---

