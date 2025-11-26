# Penetration Test Report – Unauthorized Access to /ftp (Directory Browsing)

## 1. Objective
The objective of this test was to identify a vulnerability that allows attackers to access internal or confidential files.  
The focus was on discovering unprotected directories within the OWASP Juice Shop test application.

## 2. Scope
- **Target System:** OWASP Juice Shop – local instance  
- **Testing Method:** Black‑Box  
- **Test Date:** 26.11.2025 
- **Tools:** Firefox Developer Tools, Web Browser, Burp Suite (optional)

## 3. Methodology (Step-by-Step)

### 3.1 Initial Indicators in the Application
While exploring the Juice Shop interface, I used the Scoreboard and main navigation to identify potential attack surfaces.

In the Scoreboard, a challenge appeared stating:

**"Find a confidential document."**

This suggested that there might be files stored somewhere in the webroot that were not properly protected.

### 3.2 Analysis of Publicly Loaded Files
Using the browser’s Developer Tools (F12 → Network), I observed that the application dynamically loaded Markdown files, for example:

```
/assets/public/...
```

This led to the idea of testing additional directory names typically found in development environments, such as:

- `/backup`  
- `/private`  
- `/logs`  
- `/ftp`

### 3.3 Manual Directory Guessing
I began testing potential directories by manually entering them into the browser’s URL bar.

Examples:

```
http://localhost:3000/backup
http://localhost:3000/private
```

Both returned **404 Not Found**.

However, when testing:

```
http://localhost:3000/ftp
```

the server responded with a directory index view, indicating that the folder had **public read access**.

### 3.4 Analysis of the /ftp Directory
Inside the `/ftp` directory, several files were accessible, including internal documentation such as:

- `acquisitions.md`  
- `legal.md`

These documents contained sensitive internal information (e.g., acquisition plans, legal notes) that should not be publicly visible.

The fact that the folder was accessible without authentication represents a clear security flaw.

## 4. Video Walkthrough
[Video Walkthrough](https://www.loom.com/share/16de980d25f94615bd2851370d9391b4)

## 5. Identified Vulnerability
### Broken Access Control – Unprotected Directory / Directory Browsing

**Description:**  
The `/ftp` directory is publicly accessible without authentication.  
It allows retrieving internal documentation and potentially sensitive information.

**Cause:**  
- Missing access restrictions in backend or web server configuration  
- Directory placed inside the public webroot  
- Directory listing (autoindex) enabled

## 6. Risk Analysis / Impact

### Technical Impact
- Unauthenticated access to confidential documents  
- Exposure of internal structure, file names, or processes  
- Useful information for attackers during reconnaissance  

### Business Impact
- Data leakage  
- Compliance violations  
- Potential loss of trust  
- Exposure of internal documentation to the public  

## 7. Recommendations

1. **Adjust Web Server Configuration**  
   - Move internal directories outside the public webroot.

2. **Disable Directory Listing**  
   Example (NGINX):  
   ```nginx
   autoindex off;
   ```

3. **Implement Access Controls**  
   - Require authentication before accessing internal files.

4. **Perform Regular Security Scans**  
   - Use automated tools to detect open or unprotected directories.

## 8. Conclusion
Through a combination of manual exploration, network monitoring, and targeted directory guessing, an unprotected `/ftp` directory was discovered.  
This challenge demonstrates how easily sensitive files can be exposed in development or DevOps environments due to misconfigured build or deployment pipelines.
