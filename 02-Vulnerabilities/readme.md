# SQL Injection — DVWA (Damn Vulnerable Web App)

**Date:** 2025-11-30  
**Environment:** Kali Linux (VM) running DVWA (Local: http://127.0.0.1/DVWA)  
**Tools used:** Browser, Burp Suite (Proxy), DVWA, sqlite/mysql (local DB), curl (optional)

---

## 1. Summary
SQL Injection (SQLi) is a web application vulnerability where untrusted input is incorrectly handled and injected into database queries. Successful SQLi can lead to data disclosure, authentication bypass, or remote code execution depending on the application.

This document shows how I tested for SQLi in DVWA, the steps and payloads used, example outputs, and recommended mitigations.

---

## 2. Scope
- Target application: DVWA (local instance)  
- Target page(s): DVWA → SQL Injection page (`http://127.0.0.1/DVWA/vulnerabilities/sqli/`)  
- Tools in-scope: Browser, Burp Suite (intercept & modify), DVWA low/medium security settings

---

## 3. Pre-test setup
1. Ensure DVWA is running and database initialized (see `01-Setup/installation.md`).  
2. Login to DVWA (default credentials: `admin` / `password`).  
3. Set DVWA Security to **Low** for initial testing: `DVWA → DVWA Security → low`.

---

## 4. Testing methodology (high-level)
1. Open the SQL Injection page in your browser.  
2. Intercept the request with Burp Suite or use the web form directly.  
3. Send input payloads and observe responses for error messages, data disclosure, or behaviour changes.  
4. Escalate from simple tests to union-based / boolean-based / time-based techniques if needed.  
5. Capture screenshots and record payloads used.

---

## 5. Example walkthrough (basic SQLi)

### 5.1 Access the vulnerable page
http://127.0.0.1/DVWA/vulnerabilities/sqli/

css
Copy code

The form includes a parameter `id` that retrieves user info:
http://127.0.0.1/DVWA/vulnerabilities/sqli/?id=1&Submit=Submit

markdown
Copy code

### 5.2 Test simple payloads in the `id` parameter
- Normal request (id=1) returns a user name.
- Try `id=1'` to cause a syntax error.

**Payloads tried:**
1
1'
1' OR '1'='1
1' OR '1'='1' --
1' UNION SELECT null, version(), user() --

makefile
Copy code

### 5.3 Example: Authentication / data disclosure
Send:
?id=1' OR '1'='1' --

yaml
Copy code
If the application returns multiple rows or larger data than expected, this indicates SQLi.

### 5.4 Using Burp Suite
1. Configure browser proxy to `127.0.0.1:8080`.  
2. Intercept the request for the SQLi page.  
3. Send the request to Repeater.  
4. Test payloads in Repeater and observe responses.

**Burp Repeater steps:**
- Right click intercepted request → Send to Repeater  
- Modify `id` value → Forward / Go → Observe response body for database output or errors

---

## 6. Advanced enumeration (if needed)
- **Union-based:** find number of columns and extract data:
  - `1' UNION SELECT null, null, version() --`
- **Boolean-based:** test true/false to detect blind SQLi:
  - `1' AND 1=1 --` (true) vs `1' AND 1=2 --` (false)
- **Time-based (blind):**
  - `1' OR IF(1=1, SLEEP(5), 0) --` (MySQL) — observe response delay

> ⚠️ Only run time-based tests on your own lab (DVWA) — never against systems you don't own or have permission to test.

---

## 7. Evidence (add your screenshots)
- Capture screenshots of:
  - The vulnerable form with `id` parameter
  - Burp Repeater request & response (before and after payload)
  - DVWA page output showing injected data or error messages

Place images in `/02-Vulnerabilities/images/sql-injection/` and reference them here, e.g.:

`![Burp Repeater response](./images/sql-injection/burp-repeater-response.png)`

---

## 8. Impact
- Data exposure (usernames, emails, hashed passwords)  
- Authentication bypass (depending on application)  
- Potential privilege escalation or remote code execution on poorly configured systems

---

## 9. Mitigation & Fixes
1. **Use parameterized queries / prepared statements** — do not concatenate user input into SQL strings.  
2. **Use least privilege** for DB user accounts (no unnecessary SELECT/WRITE privileges).  
3. **Input validation & output encoding** — validate types and lengths; escape as necessary.  
4. **Stored procedures** where appropriate and safe, but still use parameters.  
5. **Web application firewall (WAF)** as a defense-in-depth measure.  
6. **Error handling** — do not show database errors to users (detailed errors in logs only).

---

## 10. Sample remediation code (PHP + MySQLi prepared statement)
```php
// Example using MySQLi prepared statements
$stmt = $mysqli->prepare("SELECT first_name, last_name FROM users WHERE id = ?");
$stmt->bind_param("i", $id);
$id = intval($_GET['id']);
$stmt->execute();
$result = $stmt->get_result();
$row = $result->fetch_assoc();
11. Notes & lessons learned
Start with simple payloads and escalate.

Burp Suite Repeater is fast for iterative testing.

Keep detailed screenshots and notes for reporting.

Always work in isolated lab environments.

12. References
OWASP SQL Injection Cheat Sheet (use as a study reference)

DVWA project repo: https://github.com/digininja/DVWA

13. Next actions
Add screenshots to /02-Vulnerabilities/images/sql-injection/

Move to the next vulnerability: Cross-Site Scripting (XSS) after documenting screenshots.

yaml
Copy code

---

# ✅ After you paste & commit
1. Add screenshots later: create folder `02-Vulnerabilities/images/sql-injection/` and upload PNGs.  
2. Next file I will give you: `02-Vulnerabilities/XSS.md` (full content ready to paste).  
3. Keep one vulnerability per file — that looks very professional.

You’re doing great — tell me when you’ve committed `SQL-Injection.md` and I’ll drop the `XSS.md` content immediately.

## 11. Notes & lessons learned

Start with simple payloads and escalate.

Burp Suite Repeater is fast for iterative testing.

Keep detailed screenshots and notes for reporting.

Always work in isolated lab environments.

## 12. References

OWASP SQL Injection Cheat Sheet (use as a study reference)

DVWA project repo: https://github.com/digininja/DVWA

## 13. Next actions

Add screenshots to /02-Vulnerabilities/images/sql-injection/

Move to the next vulnerability: Cross-Site Scripting (XSS) after documenting screenshots.


---

# ✅ After you paste & commit
1. Add screenshots later: create folder `02-Vulnerabilities/images/sql-injection/` and upload PNGs.  
2. Next file I will give you: `02-Vulnerabilities/XSS.md` (full content ready to paste).  
3. Keep one vulnerability per file — that looks very professional.

You’re doing great — tell me when you’ve committed `SQL-Injection.md` and I’ll drop the `XSS.md` content immediately.

