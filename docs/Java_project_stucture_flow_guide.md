# Java Project Structure & Best Practices

## Overview
This guide explains the **standard Java project structure**, including the roles of **repositories, controllers, services, models (beans), DTOs**, and more. It ensures that all team members follow a **clean, modular architecture** for better maintainability and scalability.

---

## 1. Standard Java Project Structure
A well-organized **Java project** typically follows this structure:

```
FortisBankSystem/
 ├── src/
 │   ├── main/
 │   │   ├── java/com/fortisbank/
 │   │   │   ├── controller/ (Handles HTTP requests)
 │   │   │   ├── service/ (Business logic)
 │   │   │   ├── repository/ (Database interactions)
 │   │   │   ├── model/ (Data structures & entities)
 │   │   │   ├── dto/ (Data Transfer Objects)
 │   │   │   ├── exception/ (Custom error handling)
 │   │   │   ├── config/ (Application configurations)
 │   │   │   ├── security/ (Auth & role-based access)
 │   │   │   ├── FortisBankApplication.java (Main entry point)
 │   │   ├── resources/
 │   │   │   ├── application.properties (Config settings)
 │   ├── test/ (Unit & integration tests)
 ├── pom.xml (Maven dependencies)
```

---

## 2. Application Flow Explanation
1. **User makes an HTTP request** to a specific endpoint (`/accounts/{id}`).
2. **Controller receives the request** and delegates it to the Service layer.
3. **Service processes the request** (validations, business logic, transformations) and calls the Repository.
4. **Repository interacts with the database** to fetch or manipulate data.
5. **Service processes the data** (optional: convert to DTO) and returns it to the Controller.
6. **Controller returns the response** to the client (typically in JSON format).

This separation ensures **maintainability, scalability, and clear responsibility divisions** in the system.

---

## 3. Explanation of Key Components

### **3.1 Controller Layer** (`controller/`)
- **Handles incoming HTTP requests** (REST API endpoints).
- **Communicates with `Service` layer** for business logic.
- **Returns JSON responses** to clients.

**Example:**
```java
@RestController
@RequestMapping("/accounts")
public class AccountController {
    private final AccountService accountService;
    
    public AccountController(AccountService accountService) {
        this.accountService = accountService;
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<AccountDTO> getAccountById(@PathVariable Long id) {
        return ResponseEntity.ok(accountService.getAccountById(id));
    }
}
```

---

### **3.2 Service Layer** (`service/`)
- **Contains business logic** (data processing, validation, calculations).
- Calls **Repository layer** for database operations.

**Example:**
```java
@Service
public class AccountService {
    private final AccountRepository accountRepository;
    
    public AccountService(AccountRepository accountRepository) {
        this.accountRepository = accountRepository;
    }
    
    public AccountDTO getAccountById(Long id) {
        Account account = accountRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("Account not found"));
        return new AccountDTO(account);
    }
}
```

---

### **3.3 Repository Layer (JPA & JDBC)** (`repository/`)
#### **Using JPA (Spring Data JPA)**
- **Handles database interactions** using **JPA (or JDBC if ORM is avoided)**.
- Abstracts direct database queries for cleaner code.

**Example:**
```java
@Repository
public interface AccountRepository extends JpaRepository<Account, Long> {
    Optional<Account> findByAccountNumber(String accountNumber);
}
```

#### **Using JDBC (Raw SQL Queries)**
If using **JDBC without JPA**, manually handle connections.

**Example:**
```java
@Repository
public class AccountRepositoryJDBC {
    private final DataSource dataSource;
    
    public AccountRepositoryJDBC(DataSource dataSource) {
        this.dataSource = dataSource;
    }
    
    public Account getAccountById(Long id) throws SQLException {
        String query = "SELECT * FROM accounts WHERE id = ?";
        try (Connection conn = dataSource.getConnection();
             PreparedStatement stmt = conn.prepareStatement(query)) {
            stmt.setLong(1, id);
            ResultSet rs = stmt.executeQuery();
            if (rs.next()) {
                return new Account(rs.getLong("id"), rs.getString("accountNumber"), rs.getDouble("balance"));
            }
        }
        return null;
    }
}
```

---

### **3.4 Model Layer** (`model/`)
Defines **database entities** (tables, relationships).

**Example:**
```java
@Entity
@Table(name = "accounts")
public class Account {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String accountNumber;
    private Double balance;
}
```

---

### **3.5 DTO Layer** (`dto/` - Data Transfer Objects)
- **Prevents exposing entity models directly**.

**Example:**
```java
public class AccountDTO {
    private String accountNumber;
    private Double balance;
    
    public AccountDTO(Account account) {
        this.accountNumber = account.getAccountNumber();
        this.balance = account.getBalance();
    }
}
```

---

## 4. Coding Best Practices
- **Follow SOLID principles** for maintainability.
- **Use DTOs instead of exposing raw models**.
- **Keep controllers slim, move logic to services**.
- **Use dependency injection (`@Autowired`)** instead of manually creating objects.
- **Write meaningful logs (`SLF4J` preferred)**.
- **Write unit tests for services and repositories**.

---

