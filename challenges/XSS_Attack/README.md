# Penetration Test Report – Stored XSS via Manipulated Registration Request

## 1. Objective
The objective of this penetration test was to identify a persistent Cross‑Site Scripting (Stored XSS) vulnerability in the OWASP Juice Shop application.  
This vulnerability allows an attacker to execute arbitrary JavaScript code in the administrator’s browser when the admin views a manipulated user entry in the administration panel.

## 2. Scope
- **Target System:** OWASP Juice Shop – local instance  
- **Testing Methodology:** Black‑Box  
- **Test Period:** 26.11.2025  
- **Tools:** Browser DevTools, Burp Suite (Proxy & Repeater)

## 3. Methodology

### 3.1 Initial User Registration
1. Navigated to the login page:  
   `http://127.0.0.1:3000/#/login`  
2. Selected “Not yet a customer?”  
3. Filled in the registration form with a valid email address (e.g., `bob@gmail.com`)  
4. Submitted the registration form  

### 3.2 Manipulation of the Registration Request
- Intercepted the request in Burp Suite.  
- POST request to `/api/Users` was identified.  
- JSON body contained fields such as:

```json
{
  "email": "Marbach@dev.de",
  "password": "test123",
  "passwordRepeat": "test123",
  "securityQuestion": {
    "id": 7,
    "question": "Name of your favorite pet?"
  },
  "securityAnswer": "test123"
}
```

- Forwarded the request to the Repeater and injected the following payload into the email field:

```bash
"email": "<iframe src='javascript:alert(`xss`)'>"
```

- Server responded with `HTTP 201 Created`, confirming the malicious input was stored successfully.

### 3.3 Triggering the Stored XSS
1. Logged in as an administrator  
2. Opened the admin dashboard:  
   `http://127.0.0.1:3000/#/administration`  
3. When the list of users loaded, the manipulated email value was rendered without output escaping  
4. The `<iframe>` and `javascript:` protocol executed immediately, showing a popup with “xss” in the admin’s browser

## 4. Identified Vulnerability
**Stored Cross‑Site Scripting (XSS) via the Registration Form**  

- User input is stored without proper server-side validation  
- Admin interface renders the data without output encoding  
- Although `<script>` tags are filtered, execution is possible through an `<iframe>` with a `javascript:` URI

## 4. Video Walkthrough
→ https://www.loom.com/share/82ef9b217feb4caa86a8898663e18473

## 5. Risk Analysis / Impact

### Technical Impact
- Arbitrary JavaScript execution in the administrator’s browser  
- Access to cookies, session tokens, or local storage  
- Ability to manipulate admin-level actions  
- Potential to load additional malicious scripts

### Business Impact
- Full compromise of the administrator account  
- Manipulation of user data, orders, or shop content  
- Leakage of customer information  
- Severe reputational damage and legal consequences (e.g., GDPR violations)

## 6. Recommendations
1. **Server-Side Input Validation**  
   - Only accept RFC-compliant email addresses  
   - Reject all HTML and JavaScript fragments

2. **Input Sanitization**  
   - Remove or neutralize dangerous characters and patterns such as `<`, `>`, `"`, `'`, `javascript:`, `iframe`, etc.

3. **Output Encoding**  
   - Apply proper HTML escaping when rendering user-provided data in the admin panel

4. **Content Security Policy (CSP)**  
   - Block `javascript:` URLs  
   - Restrict where scripts can run (e.g., `script-src 'self'`)

## 7. Conclusion
The assessment demonstrated that the OWASP Juice Shop application is vulnerable to Stored XSS within the registration process.  
By modifying a single request, malicious JavaScript was injected into the database and executed automatically when an administrator accessed the user management panel.  

Given the potential for full admin compromise, this vulnerability is considered **critical**.  
Strong validation, proper output handling, and a restrictive CSP are essential to mitigate this class of attack.
