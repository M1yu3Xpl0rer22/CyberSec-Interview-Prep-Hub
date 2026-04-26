# 🔐 Cybersecurity Interview Questions & Answers

> From basic concepts to advanced scenario-based questions — including real questions asked at companies.

---

## 📑 Table of Contents

- [Level 1 — Basic Concepts](#-level-1--basic-concepts)
- [Level 2 — Intermediate](#-level-2--intermediate)
- [Level 3 — Advanced Scenario-Based](#-level-3--advanced-scenario-based)
- [Level 4 — Real Company Questions](#-level-4--real-company-questions)

---

## 🟢 Level 1 — Basic Concepts

### Networking Fundamentals

**Q1: What is the OSI model? Name all 7 layers.**
> **A:** Open Systems Interconnection model — a framework for network communication.
> `Physical → Data Link → Network → Transport → Session → Presentation → Application`
> **Interview tip:** Memorize with: *"Please Do Not Throw Sausage Pizza Away"*

**Q2: What is the difference between TCP and UDP?**
> **A:**
> - **TCP** — Connection-oriented, reliable, uses 3-way handshake (SYN → SYN-ACK → ACK). Used for HTTP, SSH, FTP.
> - **UDP** — Connectionless, faster, no guarantee of delivery. Used for DNS, VoIP, streaming.

**Q3: What is a firewall?**
> **A:** A network security device that monitors and filters incoming/outgoing traffic based on defined security rules. Types: packet-filtering, stateful inspection, proxy, NGFW.

**Q4: What is the difference between IDS and IPS?**
> **A:**
> - **IDS** (Intrusion Detection System) — Monitors and **alerts** on suspicious activity. Passive.
> - **IPS** (Intrusion Prevention System) — Monitors and **blocks** suspicious activity. Inline/active.

**Q5: What are common ports a SOC analyst should know?**
> | Port | Service | Port | Service |
> |------|---------|------|---------|
> | 21 | FTP | 443 | HTTPS |
> | 22 | SSH | 445 | SMB |
> | 23 | Telnet | 3389 | RDP |
> | 25 | SMTP | 53 | DNS |
> | 80 | HTTP | 3306 | MySQL |

**Q6: What is DNS and how does it work?**
> **A:** Domain Name System translates domain names to IP addresses. Flow: Browser → Local cache → Recursive resolver → Root server → TLD server → Authoritative server → IP returned.

**Q7: What is ARP? What is ARP spoofing?**
> **A:** ARP (Address Resolution Protocol) maps IP addresses to MAC addresses. **ARP spoofing** — attacker sends fake ARP messages to link their MAC with a legitimate IP, enabling MITM attacks.

### Security Basics

**Q8: What is the CIA Triad?**
> **A:**
> - **Confidentiality** — Only authorized access (encryption, access controls)
> - **Integrity** — Data is accurate and unaltered (hashing, checksums)
> - **Availability** — Systems are accessible when needed (redundancy, backups)

**Q9: What is the difference between symmetric and asymmetric encryption?**
> **A:**
> - **Symmetric** — Same key for encrypt/decrypt (AES, DES). Faster but key sharing is a problem.
> - **Asymmetric** — Public key encrypts, private key decrypts (RSA, ECC). Slower but solves key distribution.

**Q10: What is hashing? Is it reversible?**
> **A:** Hashing converts data into a fixed-length string. It is **one-way** (not reversible). Common: MD5 (weak), SHA-256 (strong). Used for password storage and integrity checks.

**Q11: What is the difference between a vulnerability, a threat, and a risk?**
> **A:**
> - **Vulnerability** — A weakness in a system (e.g., unpatched software)
> - **Threat** — Something that can exploit a vulnerability (e.g., attacker, malware)
> - **Risk** — The potential impact when a threat exploits a vulnerability

**Q12: What is a VPN and how does it work?**
> **A:** Virtual Private Network creates an encrypted tunnel between your device and a server, hiding your IP and encrypting traffic. Protocols: OpenVPN, WireGuard, IPSec.

**Q13: What is 2FA/MFA?**
> **A:** Multi-Factor Authentication requires 2+ verification methods: something you **know** (password), something you **have** (phone/token), something you **are** (biometric).

---

## 🟡 Level 2 — Intermediate

### Threats & Attacks

**Q14: Explain a phishing attack. How do you detect it?**
> **A:** Social engineering attack using fake emails/sites to steal credentials. Detection:
> - Check sender domain (spoofed addresses)
> - Hover over links before clicking
> - Look for urgency/fear language
> - Check email headers (SPF, DKIM, DMARC failures)

**Q15: What is the difference between XSS and CSRF?**
> **A:**
> - **XSS** (Cross-Site Scripting) — Injecting malicious scripts into web pages viewed by other users. Steals cookies/sessions.
> - **CSRF** (Cross-Site Request Forgery) — Tricks authenticated users into performing unwanted actions on a trusted site.

**Q16: What is SQL Injection?**
> **A:** Inserting malicious SQL queries via user input to manipulate databases. Example: `' OR 1=1 --` bypasses login. Prevention: parameterized queries, input validation, WAF.

**Q17: What is a DDoS attack? How do you mitigate it?**
> **A:** Distributed Denial of Service overwhelms a target with traffic from multiple sources. Mitigation: rate limiting, CDN (Cloudflare), traffic scrubbing, blackhole routing, WAF rules.

**Q18: What is a MITM attack?**
> **A:** Man-in-the-Middle — attacker intercepts communication between two parties. Prevention: HTTPS/TLS, certificate pinning, VPN, avoiding public WiFi.

**Q19: What is privilege escalation?**
> **A:** Gaining higher access than authorized. Two types:
> - **Vertical** — Normal user → admin (e.g., exploiting kernel vulnerability)
> - **Horizontal** — User A accessing User B's data

### Tools & Frameworks

**Q20: What is SIEM? Name some tools.**
> **A:** Security Information and Event Management — collects, correlates, and analyzes security logs in real-time. Tools: **Splunk**, **IBM QRadar**, **Microsoft Sentinel**, **Elastic SIEM**, **Wazuh**.

**Q21: What is the MITRE ATT&CK framework?**
> **A:** A knowledge base of adversary tactics, techniques, and procedures (TTPs) based on real-world observations. Used by SOC teams to understand attacker behavior and improve detection. 14 tactics from Initial Access to Impact.

**Q22: Explain the Cyber Kill Chain.**
> **A:** Lockheed Martin's 7-step attack model:
> `Reconnaissance → Weaponization → Delivery → Exploitation → Installation → C2 → Actions on Objectives`
> Defenders can break the chain at any step to stop the attack.

**Q23: What is the difference between IOC and IOA?**
> **A:**
> - **IOC** (Indicator of Compromise) — Evidence that a breach occurred (malicious IP, file hash, domain)
> - **IOA** (Indicator of Attack) — Real-time behavior suggesting an attack is happening (unusual login patterns, lateral movement)

**Q24: What are Windows Event Log IDs a SOC analyst should know?**
> | Event ID | Meaning |
> |----------|---------|
> | 4624 | Successful login |
> | 4625 | Failed login |
> | 4648 | Login with explicit credentials |
> | 4672 | Admin/special privileges assigned |
> | 4720 | User account created |
> | 4732 | User added to security group |
> | 1102 | Audit log cleared |
> | 7045 | New service installed |

---

## 🔴 Level 3 — Advanced Scenario-Based

> These test your **thinking process**, not just knowledge. Use the framework: **Identify → Contain → Investigate → Remediate → Document**

**Scenario 1: A user reports their computer is running slow and they see pop-ups. What do you do?**
> 1. **Isolate** the machine from the network (unplug/disable WiFi)
> 2. **Check** running processes (Task Manager / `ps aux`) for suspicious activity
> 3. **Run** antivirus/EDR scan
> 4. **Check** browser extensions and recent downloads
> 5. **Review** SIEM logs for any alerts on that endpoint
> 6. **Escalate** to L2 if malware is confirmed
> 7. **Document** in ticketing system with timeline and findings

**Scenario 2: You see 500 failed login attempts from one IP in SIEM. What's your response?**
> 1. **Identify** — Brute force attack. Check if the IP is internal or external
> 2. **Check** — Were any attempts successful? (Look for Event ID 4624 after 4625s)
> 3. **Contain** — Block the IP at firewall level
> 4. **Investigate** — Check if the targeted accounts are privileged/sensitive
> 5. **Notify** — Alert the account owners to change passwords
> 6. **Document** — Create incident report, add IP to threat intel blocklist

**Scenario 3: An employee clicked a phishing link. What do you do?**
> 1. **Contain** — Disconnect user's machine from network immediately
> 2. **Reset** — Force password reset for the user's account
> 3. **Check** — Did they enter credentials? Review login logs for suspicious access
> 4. **Scan** — Run EDR scan on the endpoint for malware/payload
> 5. **Block** — Add the phishing URL/domain to proxy and email blocklist
> 6. **Search** — Check if other users received the same email (email gateway logs)
> 7. **Report** — Document incident, notify management if data was exposed

**Scenario 4: You detect data exfiltration — large file uploads to an unknown external IP at 2 AM. What do you do?**
> 1. **Verify** — Confirm it's not a legitimate backup or cloud sync
> 2. **Contain** — Block the destination IP immediately at the firewall
> 3. **Identify** — Which user/endpoint is the source? Check EDR and SIEM
> 4. **Preserve** — Take a memory dump and disk image for forensics
> 5. **Investigate** — What data was transferred? Check DLP logs if available
> 6. **Escalate** — Notify IR team lead and management (potential breach)
> 7. **Legal** — If PII/sensitive data, prepare for compliance reporting (GDPR, etc.)

**Scenario 5: Ransomware is detected on 3 endpoints. How do you respond?**
> 1. **Isolate** — Disconnect all 3 machines from network IMMEDIATELY
> 2. **Assess scope** — Check SIEM/EDR for lateral movement to other endpoints
> 3. **Identify** — What ransomware variant? Check ransom note, file extensions, hash on VirusTotal
> 4. **Preserve** — Image affected drives before any remediation
> 5. **Check backups** — Are clean backups available? When was the last good backup?
> 6. **Do NOT pay ransom** — No guarantee of decryption, funds criminal activity
> 7. **Eradicate** — Remove malware, patch the entry point
> 8. **Restore** — Rebuild from clean images + restore data from backups
> 9. **Report** — Full incident report, update detection rules

**Scenario 6: A new zero-day vulnerability is published for software your org uses. What now?**
> 1. **Assess** — Is the vulnerable software deployed in our environment? What version?
> 2. **Check exposure** — Is it internet-facing or internal only?
> 3. **Mitigate** — Apply vendor workarounds (disable feature, restrict access, WAF rules)
> 4. **Monitor** — Create custom SIEM rules to detect exploitation attempts
> 5. **Patch** — Apply the official patch ASAP when released
> 6. **Communicate** — Notify stakeholders about the risk and mitigation steps

---

## 🏢 Level 4 — Real Company Questions

> Questions reported from actual interviews at security companies and MNCs.

### SOC Analyst Roles (TCS, Wipro, Infosys, Accenture)

| # | Question |
|---|---|
| 1 | Walk me through what happens when you type a URL in a browser. |
| 2 | How would you investigate a phishing email reported by a user? |
| 3 | What SIEM tool have you worked with? Show me how you'd write a query for failed logins. |
| 4 | Difference between a false positive and a false negative. Which is more dangerous? |
| 5 | Explain the incident response lifecycle. |
| 6 | What is the 3-way handshake? What happens if the final ACK is never sent? |
| 7 | How do you differentiate between a real alert and noise in SIEM? |
| 8 | What are SPF, DKIM, and DMARC? Why do they matter? |
| 9 | You see a user logging in from India and the US within 5 minutes. What do you do? |
| 10 | What would you check first if a server is compromised? |

### Security Engineer / Mid-Level (Deloitte, PwC, EY, KPMG)

| # | Question |
|---|---|
| 1 | Explain how TLS/SSL handshake works. |
| 2 | How would you harden a Linux server? |
| 3 | What is the difference between HIDS and NIDS? |
| 4 | Describe a time you found and fixed a security vulnerability. |
| 5 | How does certificate pinning work and why is it important? |
| 6 | What is lateral movement? How do you detect it? |
| 7 | Explain OWASP Top 10. Which is most critical and why? |
| 8 | How would you set up monitoring for a cloud environment (AWS/Azure)? |
| 9 | What's the difference between penetration testing and vulnerability assessment? |
| 10 | How do you handle a situation where a C-level executive's account is compromised? |

### Product Security / Startups

| # | Question |
|---|---|
| 1 | How would you secure a REST API? |
| 2 | Explain CORS. How can misconfigured CORS lead to exploitation? |
| 3 | What is SSRF? Give a real-world example. |
| 4 | How do you securely store passwords in a database? |
| 5 | Difference between authentication and authorization. |
| 6 | What security headers should every web app implement? |
| 7 | How would you perform a threat model for a new feature? |
| 8 | Explain the concept of defense in depth. |

### HR / Behavioral Questions (All Levels)

| # | Question |
|---|---|
| 1 | Why do you want to work in cybersecurity? |
| 2 | Tell me about a time you worked under pressure. |
| 3 | How do you stay updated with the latest threats? |
| 4 | Describe a security incident you handled. What did you learn? |
| 5 | How would you explain a technical vulnerability to a non-technical manager? |
| 6 | Where do you see yourself in 3 years in the security field? |

---

## 💡 Quick Answer Bank — Most Asked One-Liners

| Question | Quick Answer |
|---|---|
| What is a zero-day? | A vulnerability unknown to the vendor with no patch available |
| What is a honeypot? | A decoy system designed to attract and detect attackers |
| What is the principle of least privilege? | Users get only the minimum access needed for their job |
| What is defense in depth? | Multiple layers of security controls throughout a system |
| Encoding vs Encryption vs Hashing? | Encoding = format change (Base64), Encryption = reversible with key, Hashing = one-way |
| What is a DMZ? | A network segment between internal and external networks hosting public-facing services |
| What is NAT? | Network Address Translation — maps private IPs to public IPs |
| What is a WAF? | Web Application Firewall — filters HTTP traffic to protect web apps |
| What is SOAR? | Security Orchestration, Automation, and Response — automates IR workflows |
| What is threat hunting? | Proactively searching for threats that evade existing detection |

---

<div align="center">

**📌 Bookmark this file and review before every interview!**

[← Back to README](README.md)

</div>
