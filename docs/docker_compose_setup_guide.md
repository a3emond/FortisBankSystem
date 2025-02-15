# Docker Compose Setup for Fortis Bank System

## Overview
This `docker-compose.yml` file sets up all necessary services for the **Fortis Bank System**, including:
- **Oracle XE Database** with persistent storage
- **Tomcat Server** configured for **Servlets and JSP support**
- **Environment variables** for configuration flexibility
- **Automatic volume creation** if not present

---

## Prerequisites
Ensure **Docker** and **Docker Compose** are installed on your machine.

---

## Docker Compose Configuration

### **`docker-compose.yml`**
```yaml
version: '3.8'

services:
  oracle-db:
    image: gvenzl/oracle-xe:latest
    container_name: oracle-db
    environment:
      ORACLE_PASSWORD: "${DB_PASSWORD:-admin}"
      ORACLE_ALLOW_REMOTE: "true"
    ports:
      - "1521:1521"
    volumes:
      - oracle_data:/opt/oracle/oradata
    restart: unless-stopped

  tomcat-server:
    image: tomcat:latest
    container_name: tomcat-server
    environment:
      CATALINA_OPTS: "-Denv=production"
    ports:
      - "8080:8080"
    volumes:
      - ./webapps:/usr/local/tomcat/webapps
      - ./conf/tomcat-users.xml:/usr/local/tomcat/conf/tomcat-users.xml
    restart: unless-stopped

volumes:
  oracle_data:
    driver: local
```

---

## Environment Variables
To customize configuration, create a `.env` file:

### **`.env`**
```env
DB_PASSWORD=your_secure_password
TOMCAT_PORT=8080
```

---

## Additional Configurations

### **Tomcat User Configuration**
Create a **`conf/tomcat-users.xml`** file for user authentication and management:
```xml
<tomcat-users>
    <user username="admin" password="admin" roles="manager-gui,admin-gui"/>
</tomcat-users>
```

### **Automatic Volume Creation**
Ensure the `webapps` directory exists before running Docker Compose:
```bash
mkdir -p webapps conf
```

---

## Running the Services
To start all services:
```bash
docker-compose up -d
```

To stop services:
```bash
docker-compose down
```

---
