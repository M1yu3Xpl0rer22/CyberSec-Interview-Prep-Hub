# 🛡️ Application Security Questions & Answers

> Always mention **remediation** alongside the vulnerability. Interviewers love that.

[← Back to README](../README.md) · [← Previous: Pentesting](04-pentesting.md)

---

### Q1: What is OWASP? Name vulnerabilities from OWASP Top 10.

**A:** **Open Web Application Security Project** — A nonprofit focused on web application security. The **OWASP Top 10** (2021) lists the most critical web app risks:

1. **Broken Access Control** — Users accessing unauthorized resources
2. **Cryptographic Failures** — Weak encryption, exposed sensitive data
3. **Injection** — SQLi, XSS, command injection
4. **Insecure Design** — Flawed architecture/logic
5. **Security Misconfiguration** — Default creds, open cloud storage
6. **Vulnerable Components** — Outdated libraries with known CVEs
7. **Auth Failures** — Broken login, session management
8. **Software & Data Integrity Failures** — Untrusted updates, CI/CD issues
9. **Logging & Monitoring Failures** — No detection capability
10. **SSRF** — Server-Side Request Forgery

---

### Q2: VA vs Pentesting vs Red Teaming?

| Feature | VA | Pentesting | Red Teaming |
|---|---|---|---|
| **Goal** | Find vulnerabilities | Exploit vulnerabilities | Simulate real adversary |
| **Scope** | Broad, all assets | Defined scope | Full org (people, process, tech) |
| **Duration** | Hours-days | Days-weeks | Weeks-months |
| **Stealth** | Not needed | Some | Full stealth required |
| **Output** | Vuln list | Exploit proof + impact | Full attack narrative |

---

### Q3: Authentication vs Authorization?

**A:**
- **Authentication** (AuthN) — *"Who are you?"* — Verifying identity (login, password, biometric)
- **Authorization** (AuthZ) — *"What can you do?"* — Verifying permissions (admin vs user, read vs write)

> Authentication always comes first. You authenticate, then get authorized.

---

### Q4: Stateful vs Stateless in HTTP?

**A:**
- **Stateless** — HTTP itself is stateless. Each request is independent; server doesn't remember previous requests.
- **Stateful** — Applications add state via **cookies, sessions, tokens (JWT)**.

**How HTTP handles state:**
- **Cookies** — Server sends `Set-Cookie`, browser includes it in subsequent requests
- **Sessions** — Server stores state, sends session ID via cookie
- **Tokens (JWT)** — Self-contained tokens with user data, stored client-side

---

### Q5: What is XSS? Types?

**A:** **Cross-Site Scripting** — Injecting malicious JavaScript into web pages viewed by other users.

| Type | How It Works | Storage | Detection |
|---|---|---|---|
| **Stored XSS** | Script saved in database, served to all users | Server-side | Easier |
| **Reflected XSS** | Script in URL, reflected in response | Not stored | Medium |
| **DOM XSS** | Script manipulates DOM client-side | Client-side | **Hardest** |

> **DOM XSS is hardest to detect** because the payload never reaches the server — it executes entirely in the browser.

**Remediation:** Input validation, output encoding, CSP headers, HttpOnly cookies.

---

### Q6: Blacklisting vs Whitelisting approach?

**A:** **Whitelisting is preferred.**
- **Blacklisting** — Block known bad inputs. Problem: attackers find new bypasses constantly.
- **Whitelisting** — Allow only known good inputs. Much stronger — anything unexpected is rejected.

> Example: For a name field, whitelist `[a-zA-Z\s]` rather than trying to blacklist every possible attack character.

---

### Q7: What is CSRF? Impact? Remediation?

**A:** **Cross-Site Request Forgery** — Tricks an authenticated user's browser into making unwanted requests to a trusted site.

**Example:** User is logged into bank → visits malicious site → hidden form auto-submits a money transfer request using the user's existing session.

**Impact:** Unauthorized actions (transfers, password changes, data modification)

**Remediation:**
- **CSRF tokens** (unique per session/request)
- **SameSite cookie attribute** (`SameSite=Strict` or `Lax`)
- **Check Referer/Origin headers**
- **Re-authentication for sensitive actions**

---

### Q8: CSRF with PUT method — possible?

**A:** **More difficult but not impossible.** PUT requests are not sent by HTML forms (forms only support GET/POST). However:
- JavaScript (`fetch`/`XMLHttpRequest`) can send PUT requests
- If CORS is misconfigured, cross-origin PUT is possible
- **Without CSRF prevention + misconfigured CORS** = exploitable

---

### Q9: How to identify the web server stack?

**A:**
- **HTTP Response Headers** — `Server: Apache/2.4.41`, `X-Powered-By: PHP/7.4`
- **Error pages** — Default error pages reveal server type
- **Wappalyzer** — Browser extension that identifies tech stack
- **Nmap:** `nmap -sV --script http-headers target.com`
- **File extensions** — `.aspx` = IIS, `.php` = Apache/Nginx, `.jsp` = Tomcat
- **Case sensitivity** — Windows/IIS is case-insensitive, Linux/Apache is case-sensitive

---

### Q10: What is SQL Injection? Types?

**A:** Inserting malicious SQL queries through user input to manipulate the database.

**Types:**
- **Union-Based** — Uses `UNION SELECT` to extract data from other tables
- **Error-Based** — Extracts data from database error messages
- **Blind Boolean** — Asks true/false questions via queries, observes response changes
- **Time-Based Blind** — Uses `SLEEP()` to infer data based on response delay
- **Out-of-Band** — Data exfiltrated via DNS/HTTP to attacker-controlled server
- **2nd Order SQLi** — Payload stored in DB, triggered later in a different query

**Remediation:** Parameterized queries, prepared statements, input validation, WAF, least privilege DB accounts.

---

### Q11: Prepared Statements & Parameterized Queries?

**A:** A technique where SQL query structure is defined **separately** from user input. The database treats input as data, **never as executable code**.

```python
# VULNERABLE
query = f"SELECT * FROM users WHERE name = '{user_input}'"

# SAFE - Parameterized
cursor.execute("SELECT * FROM users WHERE name = %s", (user_input,))
```

> This is the **#1 defense** against SQL injection.

---

### Q12: How to store passwords in a database?

**A:** **Never store plaintext.** Correct approach:

```
1. Hash with a slow algorithm (bcrypt, scrypt, Argon2)
2. Add a unique salt per user (bcrypt does this automatically)
3. Use a high work factor (cost factor)
```

❌ `MD5("password")` — Weak, fast, rainbow table vulnerable
❌ `SHA256("password")` — Fast, no salt
✅ `bcrypt("password", salt, cost=12)` — Slow, salted, secure

---

### Q13: What is RCE?

**A:** **Remote Code Execution** — Attacker executes arbitrary code on the target server remotely. The most critical vulnerability class.

**Test for RCE:**
- Command injection payloads: `; ls`, `| whoami`, `` `id` ``
- Deserialization attacks
- File upload → webshell
- Template injection (SSTI)

**Remediation:** Input validation, disable dangerous functions, sandboxing, WAF, least privilege.

---

### Q14: CORS and SOP?

**A:**
- **SOP** (Same-Origin Policy) — Browser restricts scripts from accessing resources on a different origin (protocol + domain + port).
- **CORS** (Cross-Origin Resource Sharing) — A mechanism to **relax** SOP, allowing controlled cross-origin requests via HTTP headers.

```
Access-Control-Allow-Origin: https://trusted.com
Access-Control-Allow-Methods: GET, POST
Access-Control-Allow-Credentials: true
```

> **Misconfigured CORS** (e.g., `Access-Control-Allow-Origin: *` with credentials) = security risk.

---

### Q15: Does CORS protect against CSRF?

**A:** **No, not directly.** CORS controls what the browser does with the **response**, but the **request is still sent**. A CSRF attack only needs the request to be made — it doesn't need to read the response.

> CSRF tokens and SameSite cookies are the proper CSRF defenses.

---

### Q16: What is XXE?

**A:** **XML External Entity** — Exploits XML parsers that process external entity references.

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<data>&xxe;</data>
```

**Impact:** Read local files, SSRF, denial of service, port scanning
**Cause:** XML parser allows external entity processing
**Remediation:** Disable DTD processing, disable external entities, use JSON instead of XML

---

### Q17: Security Headers?

**Request Headers:**
| Header | Purpose |
|---|---|
| `Authorization` | Auth credentials (Bearer tokens, Basic auth) |
| `Cookie` | Session data |
| `Origin` | Where the request originated (CORS) |

**Response Headers:**
| Header | Purpose |
|---|---|
| `Content-Security-Policy` | Controls which resources browser can load |
| `X-Frame-Options` | Prevents clickjacking (`DENY`, `SAMEORIGIN`) |
| `X-Content-Type-Options` | Prevents MIME sniffing (`nosniff`) |
| `Strict-Transport-Security` | Forces HTTPS (HSTS) |
| `X-XSS-Protection` | Legacy XSS filter (deprecated, use CSP) |
| `Referrer-Policy` | Controls referrer info sent |
| `Permissions-Policy` | Controls browser features (camera, mic) |

---

### Q18: HTTP Methods?

| Method | Purpose | Safe? | Idempotent? |
|---|---|---|---|
| **GET** | Retrieve data | ✅ | ✅ |
| **POST** | Create/submit data | ❌ | ❌ |
| **PUT** | Update/replace entire resource | ❌ | ✅ |
| **PATCH** | Partial update | ❌ | ❌ |
| **DELETE** | Remove resource | ❌ | ✅ |
| **HEAD** | Like GET but no body (headers only) | ✅ | ✅ |
| **OPTIONS** | Check allowed methods (CORS preflight) | ✅ | ✅ |

---

### Q19: What is CSP?

**A:** **Content Security Policy** — HTTP header that tells the browser **which sources are allowed** to load content (scripts, styles, images, etc.).

```
Content-Security-Policy: default-src 'self'; script-src 'self' https://cdn.example.com; style-src 'self' 'unsafe-inline'
```

**Prevents:** XSS (blocks inline scripts), data injection, clickjacking
**Key directives:** `default-src`, `script-src`, `style-src`, `img-src`, `frame-ancestors`

---

### Q20: Race Condition? How to test?

**A:** When two or more operations try to access/modify shared data simultaneously, leading to unexpected behavior.

**Example:** User with $100 balance sends two $100 withdrawal requests simultaneously → both pass balance check → $200 withdrawn.

**Test:** Send concurrent requests using tools like **Burp Suite Turbo Intruder**, **race-the-web**, or custom threading scripts.

**Remediation:** Mutex locks, database transactions, atomic operations, optimistic locking.

---

### Q21: Cookie Attributes/Flags?

| Attribute | Purpose |
|---|---|
| `HttpOnly` | Cookie inaccessible to JavaScript → prevents XSS cookie theft |
| `Secure` | Cookie only sent over HTTPS |
| `SameSite=Strict` | Cookie never sent cross-origin |
| `SameSite=Lax` | Cookie sent on top-level navigation only |
| `Domain` | Which domains can receive the cookie |
| `Path` | URL path scope for the cookie |
| `Expires/Max-Age` | Cookie lifetime |

---

### Q22: What is Threat Modeling? STRIDE?

**A:** A structured process to identify, quantify, and address security threats during design phase.

**STRIDE** (Microsoft's threat model):
| Letter | Threat | CIA Violated |
|---|---|---|
| **S** | Spoofing | Authentication |
| **T** | Tampering | Integrity |
| **R** | Repudiation | Non-repudiation |
| **I** | Information Disclosure | Confidentiality |
| **D** | Denial of Service | Availability |
| **E** | Elevation of Privilege | Authorization |

---

### Q23: When to engage with developers in SDLC?

**A:** **As early as possible** — "Shift Left" approach:

```
Requirements  → Threat modeling, security requirements
Design        → Architecture review, STRIDE analysis
Development   → Secure coding guidelines, code review
Testing       → SAST, DAST, pentesting
Deployment    → Security config review, CI/CD pipeline checks
Maintenance   → Vulnerability management, patching
```

> Finding a bug in design costs **100x less** than fixing it in production.

---

### Q24: CI/CD Pipeline — Security role?

**A:** Integrate security checks throughout the pipeline:

```
Code Commit → SAST (static analysis) → Build → DAST (dynamic testing)
→ SCA (dependency check) → Container Scan → Deploy → Monitoring
```

**Tools:** SonarQube (SAST), OWASP ZAP (DAST), Snyk/Dependabot (SCA), Trivy (container scanning)

> This is called **DevSecOps** — security baked into CI/CD, not bolted on after.

---

### Q25: NGINX reverse proxy — what can go wrong?

**A:** (Microservice architecture with subdomains via NGINX):
- **SSRF** — If one service can make requests to internal services
- **Host Header Injection** — Manipulating Host header to access wrong backend
- **Subdomain takeover** — If a backend service is decommissioned but NGINX still routes
- **Shared cookies** — Cookies scoped to parent domain accessible across all subdomains
- **Misconfigured CORS** — One service's CORS allowing cross-origin from other subdomains
- **Path traversal** — Accessing unintended backend paths through NGINX misconfig

---

### Q26: XSS attempt shows blank popup — why?

**A:** The **`HttpOnly`** flag is set on the session cookie. When the attacker uses `document.cookie` to extract it, it returns empty because `HttpOnly` cookies are **not accessible via JavaScript**.

> This is exactly why `HttpOnly` is a critical cookie attribute.

---

<div align="center">

[← Previous: Pentesting](04-pentesting.md) · [Next: SOC & Incident Response →](06-soc-ir-dfir.md) · [← Back to README](../README.md)

</div>
