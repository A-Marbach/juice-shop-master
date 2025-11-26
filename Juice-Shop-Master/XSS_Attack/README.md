Penetration Test Report – Stored XSS via Manipulated Registration Request
1. Objective

The objective of this penetration test was to identify a persistent Cross‑Site Scripting (Stored XSS) vulnerability in the OWASP Juice Shop application.
This vulnerability allows an attacker to execute arbitrary JavaScript code in the administrator’s browser when the admin views a manipulated user entry in the administration panel.

2. Scope

Target System: OWASP Juice Shop – local instance
Testing Methodology: Black‑Box
Test Period: 26.11.2025
Tools: Browser DevTools, Burp Suite (Proxy & Repeater)

3. Methodology
3.1 Initial User Registration

Navigated to the login page:
http://127.0.0.1:3000/#/login

Selected “Not yet a customer?”

Filled in the registration form with a valid email address (e.g., bob@gmail.com)

Submitted the registration form

3.2 Manipulation of the Registration Request

After intercepting the request in Burp Suite, the POST request to:

/api/Users


was identified. The JSON body contained fields such as:

{
  "email": "bob@gmail.com",
  "password": "hallo123",
  "passwordRepeat": "hallo123",
  "securityQuestion": {
    "id": 7,
    "question": "Name of your favorite pet?"
  },
  "securityAnswer": "zaya"
}


The request was forwarded to the Repeater, and the following malicious payload was injected into the email field:
```bash
"email": "<iframe src='javascript:alert(`xss`)'>"
```

Upon sending the modified request, the server responded with HTTP 201 Created, confirming that the malicious input had been successfully stored in the database.

3.3 Triggering the Stored XSS

Logged in as an administrator

Opened the admin dashboard:
http://127.0.0.1:3000/#/administration

When the list of users loaded, the manipulated email value was rendered without output escaping

Because the iframe tag and the javascript: protocol were executed by the browser, the embedded JavaScript executed immediately.

Result:
A popup window displaying “xss” appeared in the admin’s browser.

4. Identified Vulnerability
Stored Cross‑Site Scripting (XSS) via the Registration Form

User input is stored without proper server‑side validation

The admin interface renders that data without output encoding

Although <script> tags are filtered, execution was possible through an <iframe> combined with a javascript: URI

5. Risk Analysis / Impact
Technical Impact

Arbitrary JavaScript execution in the administrator’s browser

Access to cookies, session tokens, or local storage

Ability to manipulate admin‑level actions

Potential for loading additional malicious scripts

Business Impact

Full compromise of the administrator account

Manipulation of user data, orders, or shop content

Leakage of customer information

Severe reputational damage and legal consequences (e.g., GDPR violations)

6. Recommendations
1. Server‑Side Input Validation

Only accept RFC‑compliant email addresses

Reject all HTML and JavaScript fragments

2. Input Sanitization

Remove or neutralize dangerous characters and patterns such as:
<, >, ", ', javascript:, iframe, etc.

3. Output Encoding

Apply proper HTML escaping when rendering user‑provided data in the admin panel

4. Content Security Policy (CSP)

Block javascript: URLs

Restrict where scripts can run (e.g., script-src 'self')

These combined measures would fully prevent this type of attack.

7. Conclusion

The assessment demonstrated that the OWASP Juice Shop application is vulnerable to Stored XSS within the registration process.
By modifying a single request, malicious JavaScript was injected into the database and executed automatically when an administrator accessed the user management panel.

Given the potential for full admin compromise, this vulnerability is considered critical.
Strong validation, proper output handling, and the use of a restrictive CSP are essential to mitigate this class of attack.