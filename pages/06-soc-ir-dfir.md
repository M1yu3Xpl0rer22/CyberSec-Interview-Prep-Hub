# 🔵 SOC Analyst | Incident Response | DFIR — Q&A

> Answer with **scenarios and response actions**. Cover all possible paths you can think of.

[← Back to README](../README.md) · [← Previous: Application Security](05-application-security.md)

---

### Q1: Where is the password stored in Windows?

**A:** In the **SAM** (Security Accounts Manager) file: `C:\Windows\System32\config\SAM`
- Stores NTLM password hashes
- File is locked while OS is running
- Can be accessed via: booting from USB, shadow copies, or dumping from memory (LSASS) using **Mimikatz**
- AD passwords stored in **NTDS.dit** on Domain Controllers

---

### Q2: How to read the SAM file? How does it store passwords?

**A:**
- **While OS running:** Use Mimikatz (`sekurlsa::logonpasswords`), or dump LSASS process
- **Offline:** Boot from USB Linux, mount the drive, copy SAM + SYSTEM files
- **Shadow copies:** `vssadmin list shadows` → access SAM from shadow copy

SAM stores passwords as **NTLM hashes** (MD4 of UTF-16LE encoded password). No salt in NTLM — same password = same hash across systems.

---

### Q3: Methods to crack Windows passwords?

**A:**
- **Mimikatz** — Dump hashes from LSASS memory
- **Pass-the-Hash** — Use hash directly without cracking
- **Hashcat/John the Ripper** — Offline hash cracking (dictionary, brute force, rules)
- **ophcrack** — Rainbow table-based cracking for NTLM
- **SAM extraction** — Boot from USB, copy SAM + SYSTEM, extract with `samdump2`
- **Responder** — Capture NTLMv2 hashes on network, crack offline

---

### Q4: Linux passwords — where stored? Which hash?

**A:**
- **`/etc/passwd`** — User info (readable by all, no passwords anymore)
- **`/etc/shadow`** — Password hashes (root access only)
- **Hash format:** `$id$salt$hash`
  - `$1$` = MD5
  - `$5$` = SHA-256
  - `$6$` = SHA-512 (most common)
  - `$y$` = yescrypt (modern distros)

---

### Q5: How to detect malicious activity around SAM/shadow files?

**A:**
**Windows (SAM):**
- Monitor Event ID **4663** (file access attempt on SAM)
- Monitor for Mimikatz-related events (Event ID 10 — process access to LSASS)
- Enable **Credential Guard** and **LSA Protection**
- Alert on shadow copy creation (`vssadmin` usage)

**Linux (shadow):**
- Monitor `/etc/shadow` access with **auditd**: `auditctl -w /etc/shadow -p rwa`
- Alert on `cat`, `cp`, or unusual reads of `/etc/shadow`
- Monitor for `unshadow`, `john`, `hashcat` processes

---

### Q6: What is Incident Response?

**A:** A structured approach to handling security incidents to minimize damage and recovery time.

**NIST IR Lifecycle (SP 800-61):**
```
1. Preparation           → Policies, tools, team, playbooks
2. Detection & Analysis  → Identify and confirm the incident
3. Containment           → Stop the spread (short-term + long-term)
4. Eradication           → Remove the threat
5. Recovery              → Restore systems to normal
6. Lessons Learned       → Post-incident review, improve defenses
```

---

### Q7: What is SLA?

**A:** **Service Level Agreement** — A contract defining expected response/resolution times for incidents.

| Priority | Response Time | Resolution Time | Example |
|---|---|---|---|
| **P0/P1** (Critical) | 15-30 min | 4 hours | Ransomware, active breach |
| **P2** (High) | 1 hour | 8 hours | Compromised account |
| **P3** (Medium) | 4 hours | 24 hours | Phishing email reported |
| **P4** (Low) | 24 hours | 72 hours | Policy violation |

> **P0/P1 always gets priority** — active threats that cause business impact.

---

### Q8: IOC vs IOA?

**A:**
- **IOC** (Indicators of Compromise) — Evidence that a breach **has occurred** (forensic artifacts)
  - Malicious IP, file hash, domain, registry key, email address
- **IOA** (Indicators of Attack) — Real-time behavior suggesting an attack **is happening**
  - Unusual process spawning, lateral movement, privilege escalation attempts

> **IOA is proactive** (detect in progress), **IOC is reactive** (detect after the fact).

---

### Q9: How to determine if an email is phishing?

**A:** Check these systematically:

| Check | What to Look For |
|---|---|
| **Sender** | Spoofed domain? Typosquatting? (micros0ft.com vs microsoft.com) |
| **Headers** | SPF/DKIM/DMARC failures in email headers |
| **Links** | Hover to check actual URL. Shortened links? Lookalike domains? |
| **Attachments** | Suspicious file types (.exe, .js, .scr, macro-enabled docs) |
| **Language** | Urgency, threats, grammatical errors |
| **Reply-To** | Different from sender address? |
| **Branding** | Poor logo quality, wrong colors, inconsistent formatting |

---

### Q10: User clicked phishing link + shared credentials. Actions?

**A:**
```
1. CONTAIN    → Reset user's password IMMEDIATELY
2. CONTAIN    → Revoke all active sessions/tokens
3. CONTAIN    → Disable account temporarily if compromised
4. ASSESS     → Check login logs — any unauthorized access after credential sharing?
5. ASSESS     → Check email rules — any forwarding rules added by attacker?
6. SCAN       → Run EDR scan on user's endpoint for malware
7. BLOCK      → Add phishing URL/domain to blocklist (proxy, email gateway)
8. SEARCH     → Check if other users received the same email
9. NOTIFY     → Inform the user, their manager, and IR team
10. DOCUMENT  → Full incident report with timeline
```

---

### Q11: SPF, DKIM, DMARC — what are they?

**A:** Email authentication mechanisms to prevent spoofing:

| Mechanism | Purpose |
|---|---|
| **SPF** | Specifies which mail servers can send email for a domain (DNS TXT record) |
| **DKIM** | Adds a digital signature to verify email wasn't tampered with |
| **DMARC** | Policy that tells receivers what to do if SPF/DKIM fail (none, quarantine, reject) |

```
SPF  → "Is this server allowed to send for this domain?"
DKIM → "Was this email modified in transit?"
DMARC → "What should I do if checks fail?"
```

---

### Q12: BEC (Business Email Compromise) Playbook?

**A:**
```
DETECTION
├── Alert from email gateway or user report
├── Verify: Is this a spoofed exec email? Compromised account?

ANALYSIS
├── Check email headers (SPF/DKIM/DMARC results)
├── Identify scope — who received it? Did anyone act on it?
├── Check for financial transactions or data shared

CONTAINMENT
├── Block sender address/domain
├── If account compromised → reset password, revoke sessions
├── Notify finance team to halt any pending wire transfers

ERADICATION
├── Remove malicious emails from all mailboxes (message trace + purge)
├── Check for mail forwarding rules added by attacker
├── Review OAuth app permissions

RECOVERY
├── Re-enable accounts with new credentials + MFA
├── Verify no persistent access remains

POST-INCIDENT
├── User awareness training on BEC
├── Implement stricter email policies
├── Document lessons learned
```

---

### Q13: Malware Persistence Techniques?

**A:**
| Technique | Description |
|---|---|
| **Registry Run Keys** | `HKLM\Software\Microsoft\Windows\CurrentVersion\Run` |
| **Scheduled Tasks** | `schtasks /create` — runs malware on schedule |
| **Services** | Register as Windows service (Event ID 7045) |
| **Startup Folder** | Place in `shell:startup` |
| **DLL Hijacking** | Replace legitimate DLL with malicious one |
| **WMI Event Subscription** | Persistent through WMI |
| **Cron Jobs (Linux)** | `/etc/crontab`, user crontabs |
| **Bashrc/Profile (Linux)** | Execute on user login |
| **Bootkit/Rootkit** | Survive OS reinstall (firmware level) |

---

### Q14: Process Injection? Methods?

**A:** Injecting malicious code into a running legitimate process to evade detection.

- **Classic DLL Injection** — Force process to load malicious DLL via `CreateRemoteThread`
- **Process Hollowing** — Start legitimate process suspended, replace its memory with malicious code
- **Thread Hijacking** — Hijack existing thread in target process
- **APC Injection** — Queue malicious code via Asynchronous Procedure Calls
- **AtomBombing** — Uses Windows atom tables for injection

> Used for **defense evasion** — malicious code runs under a trusted process name (e.g., `svchost.exe`).

---

### Q15: EDR vs Antivirus?

| Feature | Antivirus | EDR |
|---|---|---|
| **Detection** | Signature-based (mostly) | Behavioral + signatures + ML |
| **Visibility** | File scanning only | Process, network, registry, file, memory |
| **Response** | Quarantine/delete | Isolate endpoint, kill process, forensics |
| **Telemetry** | None | Full endpoint telemetry to central console |
| **Threat Hunting** | ❌ | ✅ Query historical data |
| **Examples** | Windows Defender, Avast | CrowdStrike, SentinelOne, Carbon Black |

---

### Q16: Ransomware — Indicators and Response?

**Indicators:**
- Mass file encryption (changed extensions: `.locked`, `.encrypted`)
- Ransom note files (`README.txt`, `DECRYPT_FILES.html`)
- Disabled shadow copies (`vssadmin delete shadows`)
- High CPU/disk usage (encryption in progress)
- C2 communication to known ransomware infrastructure

**Response:**
```
1. ISOLATE     → Disconnect affected machines immediately
2. ASSESS      → How many systems? Which data? Check for lateral movement
3. IDENTIFY    → Ransomware variant (ID Ransomware, VirusTotal)
4. PRESERVE    → Image affected drives for forensics
5. DO NOT PAY  → No guarantee of decryption
6. CHECK       → Are clean backups available? Test restoration
7. ERADICATE   → Remove malware, patch entry point
8. RESTORE     → Rebuild from clean images + backup data
9. REPORT      → Full incident report, law enforcement if needed
10. IMPROVE    → Update detection rules, segment network, improve backups
```

---

### Q17: DDoS Response Plan?

**A:**
```
1. CONFIRM     → Is it really DDoS or just high legitimate traffic?
2. IDENTIFY    → Type: Volumetric? Protocol? Application layer?
3. ACTIVATE    → Enable DDoS mitigation (Cloudflare, AWS Shield, ISP scrubbing)
4. FILTER      → Apply rate limiting, geo-blocking, IP reputation filtering
5. SCALE       → Auto-scale infrastructure if possible
6. COMMUNICATE → Notify management, update status page
7. MONITOR     → Watch for secondary attacks (DDoS as distraction)
8. DOCUMENT    → Post-incident analysis, optimize rules
```

---

### Q18: Important Windows Event IDs for SOC?

| Event ID | Description | Why Important |
|---|---|---|
| **4624** | Successful login | Track who's accessing what |
| **4625** | Failed login | Brute force detection |
| **4648** | Login with explicit creds | Lateral movement indicator |
| **4672** | Special privileges assigned | Admin access monitoring |
| **4720** | User account created | Persistence check |
| **4732** | User added to security group | Privilege escalation |
| **1102** | Audit log cleared | Anti-forensics indicator |
| **7045** | New service installed | Malware persistence |
| **4688** | Process creation | Track what's executing |
| **4697** | Service installed (detailed) | Persistence tracking |

---

### Q19: DNS Records? DNS Exfiltration?

**DNS Record Types:**
| Record | Purpose |
|---|---|
| **A** | Domain → IPv4 address |
| **AAAA** | Domain → IPv6 address |
| **CNAME** | Domain alias → another domain |
| **MX** | Mail server for domain |
| **NS** | Authoritative nameserver |
| **TXT** | Arbitrary text (SPF, DKIM, verification) |
| **PTR** | IP → domain (reverse DNS) |
| **SOA** | Start of Authority (zone info) |

**DNS Exfiltration:** Encoding stolen data into DNS queries:
```
encoded-data.attacker-domain.com
```
**Detection:** Unusually long DNS queries, high volume to single domain, high entropy in subdomains, requests to uncommon TLDs.

---

### Q20: Credential Stuffing vs Password Spraying?

| Feature | Credential Stuffing | Password Spraying |
|---|---|---|
| **Method** | Many username:password pairs from breaches | One password tried across many accounts |
| **Source** | Leaked credential databases | Common passwords (Password123!) |
| **Detection** | High login volume from distributed IPs | Many failed logins across accounts, few per account |
| **Defense** | MFA, breach monitoring, credential screening | Account lockout, MFA, password policy |

**Password Spraying use-case:** Attacker tries `Summer2026!` against all employees in AD → avoids lockout by trying only 1-2 passwords per account per lockout period.

---

### Q21: Linux logs for SIEM?

| Log File | Contains |
|---|---|
| `/var/log/auth.log` | Authentication events (SSH, sudo) |
| `/var/log/syslog` | General system messages |
| `/var/log/kern.log` | Kernel messages |
| `/var/log/apache2/access.log` | Web server access logs |
| `/var/log/apache2/error.log` | Web server errors |
| `/var/log/cron` | Cron job execution |
| `/var/log/faillog` | Failed login attempts |
| `/var/log/wtmp` | Login records (`last` command) |
| `/var/log/btmp` | Failed login records (`lastb`) |

---

## Malware Analysis

### Q22: Types of Malware Analysis?

**A:**
- **Static Analysis** — Examining malware **without executing** it
  - File hashes, strings, PE headers, imports, disassembly (IDA Pro, Ghidra)
- **Dynamic Analysis** — Executing malware in a **sandbox** and observing behavior
  - Network connections, file changes, registry modifications, process activity
  - Tools: Any.Run, Cuckoo Sandbox, Joe Sandbox
- **Hybrid** — Combine both for comprehensive analysis

---

## Compliance / GRC

### Q23: What is GDPR?

**A:** **General Data Protection Regulation** — EU law governing personal data protection.

**Key points:**
- Applies to any org processing EU citizens' data (regardless of location)
- **Right to access, rectification, erasure** ("right to be forgotten")
- **72-hour breach notification** requirement
- Fines up to **€20M or 4% of global annual revenue**
- Requires **Data Protection Officer (DPO)** for large-scale processing

**Other frameworks to know:** ISO 27001, PCI DSS (payment cards), HIPAA (healthcare), SOC 2 (service orgs), NIST CSF.

---

<div align="center">

[← Previous: Application Security](05-application-security.md) · [Next: Misc Topics →](07-misc.md) · [← Back to README](../README.md)

</div>
