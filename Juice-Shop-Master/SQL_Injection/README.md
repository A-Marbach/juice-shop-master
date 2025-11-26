# Penetration Test Report – Admin Login via SQL Injection

## 1. Objective
The objective of this penetration test was to exploit a SQL Injection vulnerability in the OWASP Juice Shop login functionality in order to gain unauthorized access to the administrator account.

## 2. Scope
- **Target System:** OWASP Juice Shop – local instance  
- **Testing Method:** Black‑Box  
- **Test Date:** 26.11.2025  
- **Tools:** Firefox DevTools, Burp Suite Community Edition  

## 3. Methodology

### 3.1 Test Execution
1. Opened the login page of the Juice Shop application.  
2. Tested various SQL injection payloads in the **email** input field.  
3. Monitored server responses to determine whether the input was being interpreted as SQL code.  

### 3.2 Successful Payload
The following payload was entered in the email field:

```sql
' OR 1=1--
```

This manipulated the SQL query so that the condition always evaluates to **true**.

### 3.3 Result
After submitting the login form:

- The system automatically logged in as the **administrator**.  
- Verification steps:  
  - The username **“admin”** was displayed.  
  - The challenge **“Login Admin”** was marked as completed on the scoreboard.


## 4. Video Walkthrough
[Video Walkthrough](https://www.loom.com/share/93c07f839b944ddab61468c8c4fedf09)


## 5. Identified Vulnerability
### SQL Injection – Authentication Bypass

#### Description
The application fails to properly validate the email input. The user-controlled value is inserted directly into the SQL query:

```sql
SELECT * FROM Users 
WHERE email = '<input>' AND password = '<input>';
```

By manipulating the input, the attacker can bypass the `WHERE` clause entirely and gain access without knowing any password.

#### Root Causes
- Missing server‑side input validation  
- Lack of prepared statements  
- Direct string concatenation inside SQL queries  

## 6. Risk Analysis / Impact

### Technical Impact
- Full takeover of the administrator account  
- Access to all user accounts  
- Ability to view, modify, or delete data  
- Potential pivoting to other vulnerabilities (e.g., Stored XSS, insecure file handling)

### Business Impact
- Complete loss of confidentiality of customer data  
- Possible misuse or theft of sensitive information  
- Severe damage to company reputation and user trust  
- Legal consequences (e.g., GDPR violations)

## 7. Recommendations
1. **Use prepared statements / parameterized queries** for all SQL operations  
2. **Server-side validation and escaping** of user input  
3. **Rate limiting** on failed login attempts  
4. **Comprehensive code review** focusing on input handling and database interactions  

## 8. Conclusion
This challenge demonstrates how insecure SQL query construction can be easily exploited to gain full system access. SQL Injection remains one of the most dangerous and widespread security vulnerabilities, making secure database handling essential for any application.
