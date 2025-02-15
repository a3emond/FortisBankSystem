# Java Naming Conventions & Component Compatibility Practices

## Overview
This guide defines **standardized naming conventions** and **component compatibility practices** for the Fortis Bank System project. Adhering to these conventions ensures **consistency, readability, maintainability**, and seamless collaboration within the team.

---

## 1. Naming Conventions
### **1.1 General Naming Rules**
| Component | Naming Convention | Example |
|-----------|------------------|---------|
| Classes | PascalCase | `AccountService` |
| Interfaces | PascalCase, starts with `I` (optional) | `IAccountService` or `AccountService` |
| Methods | camelCase | `getAccountById()` |
| Variables | camelCase | `accountNumber` |
| Constants | UPPER_SNAKE_CASE | `MAX_TRANSACTION_LIMIT` |
| Enums | PascalCase for type, UPPER_SNAKE_CASE for values | `TransactionType.DEPOSIT` |
| Packages | lowercase with dots | `com.fortisbank.service` |

### **1.2 Specific Naming for Project Components**
| Component | Naming Pattern | Example |
|-----------|---------------|---------|
| Controllers | `EntityController` | `AccountController` |
| Services | `EntityService` | `AccountService` |
| Repositories | `EntityRepository` | `AccountRepository` |
| Models (Entities) | Singular nouns | `Account`, `Transaction` |
| DTOs | `EntityDTO` | `AccountDTO` |
| Exception Classes | `EntityNotFoundException` | `AccountNotFoundException` |
| Configurations | `EntityConfig` | `SecurityConfig` |
| Utility Classes | `EntityUtils` | `DateUtils` |

---

## 2. Component Compatibility & Best Practices
### **2.1 Controllers**
- **Handle HTTP requests and responses only.**
- **Do not contain business logic; delegate it to services.**
- **Use meaningful endpoint paths (RESTful).**

✅ **Good Example:**
```java
@RestController
@RequestMapping("/accounts")
public class AccountController {
    private final AccountService accountService;
    
    public AccountController(AccountService accountService) {
        this.accountService = accountService;
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<AccountDTO> getAccount(@PathVariable Long id) {
        return ResponseEntity.ok(accountService.getAccountById(id));
    }
}
```

❌ **Bad Example (Logic in Controller):**
```java
@RestController
@RequestMapping("/accounts")
public class AccountController {
    @Autowired
    private AccountRepository accountRepository;

    @GetMapping("/{id}")
    public Account getAccount(@PathVariable Long id) {
        return accountRepository.findById(id).orElseThrow(() -> new RuntimeException("Not Found"));
    }
}
```

---

### **2.2 Services**
- **Contain business logic and handle transactions.**
- **Interact with repositories for database access.**
- **Expose only required methods.**

✅ **Good Example:**
```java
@Service
public class AccountService {
    private final AccountRepository accountRepository;
    
    public AccountService(AccountRepository accountRepository) {
        this.accountRepository = accountRepository;
    }
    
    public AccountDTO getAccountById(Long id) {
        Account account = accountRepository.findById(id)
            .orElseThrow(() -> new AccountNotFoundException("Account not found"));
        return new AccountDTO(account);
    }
}
```

---

### **2.3 Repositories**
- **Contain database interaction logic.**
- **Should be abstracted away from services using interfaces.**
- **Use JPA (`JpaRepository`) or raw JDBC if needed.**

✅ **JPA Example:**
```java
@Repository
public interface AccountRepository extends JpaRepository<Account, Long> {
    Optional<Account> findByAccountNumber(String accountNumber);
}
```

✅ **JDBC Example:**
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

### **2.4 DTOs**
- **Used for API response objects.**
- **Prevents exposing direct database entities.**

✅ **Good Example:**
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

## 3. General Coding Best Practices
### **3.1 Code Readability**
- **Keep method names descriptive.**
- **Use meaningful variable names (avoid `a`, `temp`, `var1`).**
- **Keep method lengths short (preferably < 50 lines).**

### **3.2 Component Separation**
| Layer | Responsibility |
|-------|---------------|
| Controller | Handles HTTP requests and responses. Calls Service layer. |
| Service | Business logic, validation, transaction management. Calls Repository layer. |
| Repository | Direct database access (JPA/JDBC). |
| DTO | Transfers data between layers, prevents exposing DB models. |
| Model | Represents database entities. |

---

