# Juice Shop Master
Security Vulnerability Analysis & Penetration Testing (Educational)
OWASP Juice Shop: SQL Injection, XSS, Access Control | For learning only

## Table of Contents
- [Project Overview](#project-overview)
- [Quickstart](#quickstart)
- [Challenge Documentation](#challenge-documentation)
- [Challenge Videos](#challenge-videos)
- [Security Notice](#security-notice)

## Project Overview
This repository contains the documentation of three selected Juice Shop challenges, each demonstrating a different type of vulnerability.

## Quickstart
1. Open the “Juice Shop Master” folder.  
2. Navigate to the individual challenge subfolders.  
3. Open each challenge's README to view documentation and video links.

## Challenge Documentation

### 1. Unauthorized Access via Directory Browsing (/ftp)
- **Category:** Broken Access Control  
- **Description:** A publicly accessible `/ftp` directory allowed access to internal markdown files without authentication.  
- **Documentation:**  
  → [challenges/Brocken_Access_Control](./challenges/Brocken_Access_Control/README.md)
- Directory Browsing (/ftp): https://www.loom.com/share/16de980d25f94615bd2851370d9391b4

### 2. Stored XSS via Manipulated Registration Request
- **Category:** Cross-Site Scripting (Stored XSS)  
- **Description:** By modifying the registration request in Burp Suite, a payload was stored in the database and executed in the admin interface.  
- **Documentation:**  
  → [challenges/XSS_Attack](./challenges/XSS_Attack/README.md)
- Stored XSS Registration: https://www.loom.com/share/82ef9b217feb4caa86a8898663e18473

### 3. SQL Injection – Login Admin
- **Category:** Injection (SQLi)  
- **Description:** Using a classic `' OR 1=1--` payload, authentication was bypassed and the admin account was accessed.  
- **Documentation:**  
  → [challenges/SQL_Injection](./challenges/SQL_Injection/README.md)
- SQL Injection Admin Login: https://www.loom.com/share/93c07f839b944ddab61468c8c4fedf09

## Challenge Videos
Each challenge includes a short walkthrough video (max. 5 minutes) demonstrating:
- How the vulnerability works  
- How the exploit is performed  
- Why the vulnerability is dangerous  



## Security Notice
This project and all included documentation, videos, and demonstrations are strictly for **educational purposes only**.  
No real personal data, credentials, or sensitive information were used at any time.  
Always follow legal and ethical guidelines when performing security testing.
