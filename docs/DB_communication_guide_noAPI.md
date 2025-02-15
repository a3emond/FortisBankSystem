# Database Communication Guide (Without API)

## Overview
This document provides guidelines on how to interact directly with the **Oracle XE database** without using an API. This is particularly relevant for the **JSP-based desktop application**, which will connect to the database via **JDBC (Java Database Connectivity)**.

---

## Database Connection Setup

### **1. Required Dependencies (Maven)**
Ensure the following dependency is included in your `pom.xml`:
```xml
<dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ojdbc8</artifactId>
    <scope>runtime</scope>
</dependency>
```

### **2. Establishing a Connection**
```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DatabaseConnection {
    private static final String URL = "jdbc:oracle:thin:@localhost:1521:XE";
    private static final String USER = "your_username";
    private static final String PASSWORD = "your_password";

    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(URL, USER, PASSWORD);
    }
}
```

---

## Performing CRUD Operations

### **1. Create (INSERT)**
```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class AccountDAO {
    public void createAccount(int userId, String accountType, double initialDeposit) {
        String sql = "INSERT INTO accounts (user_id, account_type, balance) VALUES (?, ?, ?)";
        
        try (Connection conn = DatabaseConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            stmt.setInt(1, userId);
            stmt.setString(2, accountType);
            stmt.setDouble(3, initialDeposit);
            stmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

### **2. Read (SELECT)**
```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class AccountDAO {
    public void getAccountById(int accountId) {
        String sql = "SELECT * FROM accounts WHERE id = ?";
        
        try (Connection conn = DatabaseConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            stmt.setInt(1, accountId);
            ResultSet rs = stmt.executeQuery();
            
            if (rs.next()) {
                System.out.println("Account ID: " + rs.getInt("id"));
                System.out.println("Type: " + rs.getString("account_type"));
                System.out.println("Balance: " + rs.getDouble("balance"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

### **3. Update (UPDATE)**
```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class AccountDAO {
    public void updateAccountBalance(int accountId, double newBalance) {
        String sql = "UPDATE accounts SET balance = ? WHERE id = ?";
        
        try (Connection conn = DatabaseConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            stmt.setDouble(1, newBalance);
            stmt.setInt(2, accountId);
            stmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

### **4. Delete (DELETE)**
```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class AccountDAO {
    public void deleteAccount(int accountId) {
        String sql = "DELETE FROM accounts WHERE id = ?";
        
        try (Connection conn = DatabaseConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            stmt.setInt(1, accountId);
            stmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

---

## Error Handling & Best Practices

### **1. Handling SQL Exceptions**
Always **log exceptions** and avoid exposing database errors to users.
```java
catch (SQLException e) {
    System.err.println("Database error: " + e.getMessage());
}
```

### **2. Preventing SQL Injection**
Always use **PreparedStatements** to avoid SQL injection vulnerabilities.

### **3. Connection Pooling**
For better performance, use **HikariCP** instead of opening a new connection for each request.
```xml
<dependency>
    <groupId>com.zaxxer</groupId>
    <artifactId>HikariCP</artifactId>
    <version>4.0.3</version>
</dependency>
```

```java
import com.zaxxer.hikari.HikariDataSource;

public class DataSourceConfig {
    private static final HikariDataSource dataSource = new HikariDataSource();
    
    static {
        dataSource.setJdbcUrl("jdbc:oracle:thin:@localhost:1521:XE");
        dataSource.setUsername("your_username");
        dataSource.setPassword("your_password");
    }
    
    public static HikariDataSource getDataSource() {
        return dataSource;
    }
}
```

### **4. Using Transactions**
```java
try (Connection conn = DatabaseConnection.getConnection()) {
    conn.setAutoCommit(false);
    
    PreparedStatement stmt1 = conn.prepareStatement("UPDATE accounts SET balance = balance - ? WHERE id = ?");
    stmt1.setDouble(1, 100);
    stmt1.setInt(2, 123);
    stmt1.executeUpdate();
    
    PreparedStatement stmt2 = conn.prepareStatement("UPDATE accounts SET balance = balance + ? WHERE id = ?");
    stmt2.setDouble(1, 100);
    stmt2.setInt(2, 789);
    stmt2.executeUpdate();
    
    conn.commit();
} catch (SQLException e) {
    conn.rollback();
    e.printStackTrace();
}
```

---

