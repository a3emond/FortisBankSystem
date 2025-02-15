# Standardized API Contracts

## Overview
This document outlines the standardized API contracts for the Fortis Bank System, ensuring consistency and clear communication between the backend and frontend systems. The API follows **RESTful principles**, returning JSON responses, and uses HTTP status codes to indicate success or failure.

---

## General API Guidelines
### **Base URL**
All API requests will be prefixed with:
```
https://api.fortisbank.com/v1
```

### **Response Format**
All responses will follow a consistent JSON format:
```json
{
    "status": "success",
    "data": { ... },
    "message": "Detailed message if needed"
}
```
If an error occurs:
```json
{
    "status": "error",
    "message": "Error details"
}
```

### **Authentication & Security**
- **OAuth 2.0** will be used for authentication.
- All endpoints require a **Bearer Token** sent in the `Authorization` header:
```http
Authorization: Bearer <token>
```
- Sensitive data will be encrypted in transit.
- Rate limiting and logging will be enforced.

---

## API Endpoints

### **1. Account Management**
#### **Create Account**
- **Endpoint:** `POST /accounts`
- **Description:** Creates a new account.
- **Request Body:**
```json
{
    "userId": 123,
    "accountType": "savings",
    "initialDeposit": 1000.00
}
```
- **Response:**
```json
{
    "status": "success",
    "data": {
        "accountId": 456,
        "accountType": "savings",
        "balance": 1000.00
    }
}
```

#### **Retrieve Account Details**
- **Endpoint:** `GET /accounts/{id}`
- **Description:** Retrieves details of a specific account.
- **Response:**
```json
{
    "status": "success",
    "data": {
        "accountId": 456,
        "accountType": "savings",
        "balance": 2500.75
    }
}
```

#### **Update Account Information**
- **Endpoint:** `PUT /accounts/{id}`
- **Description:** Updates account information.
- **Request Body:**
```json
{
    "accountType": "checking"
}
```
- **Response:**
```json
{
    "status": "success",
    "message": "Account updated successfully"
}
```

#### **Delete Account**
- **Endpoint:** `DELETE /accounts/{id}`
- **Description:** Deletes an account.
- **Response:**
```json
{
    "status": "success",
    "message": "Account deleted successfully"
}
```

---

### **2. Transactions**
#### **Initiate Transaction**
- **Endpoint:** `POST /transactions`
- **Description:** Transfers funds between accounts.
- **Request Body:**
```json
{
    "fromAccount": 456,
    "toAccount": 789,
    "amount": 150.00
}
```
- **Response:**
```json
{
    "status": "success",
    "transactionId": 98765,
    "message": "Transaction successful"
}
```

#### **Retrieve Transaction History**
- **Endpoint:** `GET /transactions?accountId=456`
- **Description:** Retrieves transaction history for an account.
- **Response:**
```json
{
    "status": "success",
    "data": [
        {
            "transactionId": 98765,
            "type": "transfer",
            "amount": 150.00,
            "date": "2024-02-15T10:00:00Z"
        }
    ]
}
```

---

### **3. Authentication & User Management**
#### **User Registration**
- **Endpoint:** `POST /users/register`
- **Request Body:**
```json
{
    "username": "john_doe",
    "password": "securePass123",
    "email": "john@example.com"
}
```
- **Response:**
```json
{
    "status": "success",
    "message": "User registered successfully"
}
```

#### **User Login**
- **Endpoint:** `POST /users/login`
- **Request Body:**
```json
{
    "username": "john_doe",
    "password": "securePass123"
}
```
- **Response:**
```json
{
    "status": "success",
    "token": "JWT-TOKEN-HERE"
}
```

#### **Retrieve User Profile**
- **Endpoint:** `GET /users/profile`
- **Authentication:** Requires `Authorization: Bearer <token>`
- **Response:**
```json
{
    "status": "success",
    "data": {
        "userId": 123,
        "username": "john_doe",
        "email": "john@example.com"
    }
}
```

---

## Error Handling
All errors will return an appropriate HTTP status code:
- `400 Bad Request` → Invalid input
- `401 Unauthorized` → Authentication failed
- `403 Forbidden` → Access denied
- `404 Not Found` → Resource not found
- `500 Internal Server Error` → Unexpected server issue

Example error response:
```json
{
    "status": "error",
    "message": "Invalid credentials"
}
```

---

