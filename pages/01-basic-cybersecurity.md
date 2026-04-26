# 🟢 Basic Cyber Security Questions & Answers

> These help establish a baseline. Don't rush — interviewers often want to hear **specific keywords**.

[← Back to README](../README.md)

---

### Q1: What is the Cyber Kill Chain?

**A:** A framework by **Lockheed Martin** describing the 7 stages of a cyber attack:

```
Reconnaissance → Weaponization → Delivery → Exploitation → Installation → C2 → Actions on Objectives
```

- **Reconnaissance** — Attacker gathers info (emails, IPs, tech stack)
- **Weaponization** — Creates a payload (malware + exploit)
- **Delivery** — Sends payload (phishing email, USB, website)
- **Exploitation** — Triggers the vulnerability
- **Installation** — Malware installs on target
- **Command & Control (C2)** — Attacker establishes remote access
- **Actions on Objectives** — Data theft, encryption, destruction

> 💡 Defenders can break the chain at **any stage** to stop the attack.

---

### Q2: How can you classify roles in Cyber Security?

**A:** Cybersecurity roles broadly fall into:

| Category | Roles | Focus |
|---|---|---|
| **Blue Team** (Defense) | SOC Analyst, IR Analyst, Threat Hunter, DFIR | Detecting, monitoring, responding |
| **Red Team** (Offense) | Pentester, Red Teamer, Exploit Developer | Simulating attacks, finding vulnerabilities |
| **Purple Team** | Purple Teamer | Bridging red & blue, improving defenses |
| **Governance** | GRC Analyst, Compliance Officer, CISO | Policies, risk management, compliance |
| **Engineering** | Security Engineer, DevSecOps, Cloud Security | Building secure systems & tools |
| **Research** | Malware Analyst, Threat Intel, Vulnerability Researcher | Studying threats, reverse engineering |

---

### Q3: What is the CIA Triad?

**A:** The three core pillars of information security:

- **Confidentiality** — Only authorized users can access data (encryption, access controls, MFA)
- **Integrity** — Data is accurate and hasn't been tampered with (hashing, checksums, digital signatures)
- **Availability** — Systems and data are accessible when needed (redundancy, backups, DDoS protection)

> Every security control maps back to one or more of these.

---

### Q4: Difference between symmetric and asymmetric cryptography?

**A:**

| Feature | Symmetric | Asymmetric |
|---|---|---|
| **Keys** | Same key for encrypt & decrypt | Public key encrypts, private key decrypts |
| **Speed** | Fast | Slower |
| **Key Problem** | How to share the key securely? | Solved — public key is shared openly |
| **Examples** | AES, DES, 3DES, Blowfish | RSA, ECC, Diffie-Hellman |
| **Use Case** | Bulk data encryption | Key exchange, digital signatures, TLS |

> In practice, **TLS uses both** — asymmetric for key exchange, symmetric for data transfer.

---

### Q5: What are Ports? How many ports does a computer have?

**A:** Ports are logical endpoints for network communication. A computer has **65,535 ports** (0-65535).

| Range | Name | Examples |
|---|---|---|
| 0-1023 | **Well-known ports** | HTTP (80), HTTPS (443), SSH (22), DNS (53) |
| 1024-49151 | **Registered ports** | MySQL (3306), RDP (3389), PostgreSQL (5432) |
| 49152-65535 | **Dynamic/Ephemeral** | Used by client applications temporarily |

---

### Q6: Why is deleted data not truly gone when you delete it?

**A:** When you delete a file, the OS only removes the **pointer/reference** to the data in the file system table. The actual data remains on disk until it's **overwritten** by new data. This is why:
- **Recovery tools** (Recuva, FTK, Autopsy) can recover deleted files
- **Secure deletion** requires overwriting with random data (DoD 5220.22-M standard)
- **SSDs** are different — TRIM command can make recovery harder but not impossible
- **Forensics** relies on this to recover evidence from suspect machines

---

### Q7: What is Encryption, Encoding, Hashing?

**A:**

| Concept | Purpose | Reversible? | Key Needed? | Example |
|---|---|---|---|---|
| **Encryption** | Protect confidentiality | ✅ Yes (with key) | ✅ Yes | AES, RSA |
| **Encoding** | Data format conversion | ✅ Yes (no key) | ❌ No | Base64, URL encoding |
| **Hashing** | Integrity verification | ❌ No (one-way) | ❌ No | SHA-256, MD5, bcrypt |

> **Key difference:** Encryption is for secrecy, encoding is for compatibility, hashing is for verification.

---

### Q8: What is Salting? Why is it used?

**A:** A **salt** is a random string added to a password **before** hashing it.

```
hash("password123")           → always same hash (vulnerable to rainbow tables)
hash("password123" + "x7kQ9") → unique hash even for same password
```

**Why?**
- Prevents **rainbow table attacks** (precomputed hash lookups)
- Same password for 2 users produces **different hashes**
- Makes brute-force significantly harder

> bcrypt, scrypt, and Argon2 automatically handle salting.

---

### Q9: Would you Encrypt and Compress, or Compress and Encrypt?

**A:** **Compress first, then encrypt.**

**Why?**
- Compression reduces redundancy in data to make it smaller
- Encryption makes data appear **random** — random data cannot be compressed
- If you encrypt first, compression will have **no effect** (file stays same size)
- Compressing first → smaller file → faster encryption → smaller encrypted output

---

### Q10: Deep Web vs Dark Web?

**A:**

| Feature | Deep Web | Dark Web |
|---|---|---|
| **What** | Content not indexed by search engines | Intentionally hidden, requires special tools |
| **Access** | Regular browser + login | Requires **Tor browser** or I2P |
| **Size** | ~90% of internet | Small subset of deep web |
| **Examples** | Email inbox, bank portal, medical records | Tor hidden services (.onion sites) |
| **Legal?** | Yes | Accessing is legal, but many activities aren't |

---

### Q11: What is MITRE ATT&CK?

**A:** A globally accessible **knowledge base of adversary tactics and techniques** based on real-world observations. Maintained by MITRE Corporation.

- **14 Tactics** (the "why") — Reconnaissance, Initial Access, Execution, Persistence, Privilege Escalation, Defense Evasion, Credential Access, Discovery, Lateral Movement, Collection, C2, Exfiltration, Impact
- **Hundreds of Techniques** (the "how") — under each tactic
- Used by SOC teams to **map detections**, improve coverage, and understand attacker behavior
- Also has **ATT&CK Navigator** for visual mapping

---

### Q12: Vulnerability vs Exploit?

**A:**
- **Vulnerability** — A weakness or flaw in a system (e.g., unpatched software, misconfiguration, weak password)
- **Exploit** — Code or technique that **takes advantage** of a vulnerability to cause harm

> Example: A buffer overflow in a web server (vulnerability) + a Python script that triggers it to get shell access (exploit).

---

### Q13: Vulnerability vs Threat vs Risk?

**A:**
- **Vulnerability** — A weakness (unpatched server)
- **Threat** — An actor or event that could exploit it (hacker, malware, natural disaster)
- **Risk** — The **potential impact** when a threat exploits a vulnerability

```
Risk = Threat × Vulnerability × Impact
```

> Example: Unpatched Apache (vulnerability) + known exploit in the wild (threat) + server hosts customer PII (high impact) = **Critical Risk**

---

### Q14: Difference between VA and PT?

**A:**

| Feature | Vulnerability Assessment (VA) | Penetration Testing (PT) |
|---|---|---|
| **Goal** | Find and list vulnerabilities | Exploit vulnerabilities to prove impact |
| **Approach** | Automated scanning + manual review | Manual + automated, simulates real attacker |
| **Depth** | Broad, surface-level | Deep, targeted |
| **Output** | List of vulnerabilities with severity | Proof of compromise, attack paths |
| **Tools** | Nessus, Qualys, OpenVAS | Metasploit, Burp Suite, manual scripts |

---

### Q15: Events vs Alerts vs Incidents?

**A:**
- **Event** — Any observable occurrence in a system (user login, file access, network connection). Most events are normal.
- **Alert** — An event that matches a **detection rule** and needs attention (failed login threshold, malware signature match)
- **Incident** — A confirmed security event that **causes harm** or violates policy (data breach, ransomware, unauthorized access)

```
Events (millions) → Alerts (hundreds) → Incidents (few)
```

---

### Q16: What are APT Groups?

**A:** **Advanced Persistent Threats** — Sophisticated, well-funded threat actor groups (often state-sponsored) that target specific organizations over long periods.

| Group | Origin | Known Targets |
|---|---|---|
| APT28 (Fancy Bear) | Russia | Governments, NATO |
| APT29 (Cozy Bear) | Russia | SolarWinds, government agencies |
| APT41 (Wicked Panda) | China | Healthcare, telecom, gaming |
| Lazarus Group | North Korea | Financial institutions, crypto |

**Characteristics:** Long dwell time, custom malware, zero-days, persistence, data exfiltration.

---

### Q17: Experience with ticketing tools?

**A:** Common ticketing tools in cybersecurity:
- **ServiceNow** — Enterprise ITSM, incident management
- **Jira** — Issue tracking, used in security teams for vuln tracking
- **TheHive** — Open-source incident response platform
- **OTRS** — Open-source ticketing
- **Zendesk** — Customer support ticketing

> Tailor your answer to tools you've actually used. Mention how you create, assign, escalate, and close tickets with proper documentation.

---

<div align="center">

[Next: Network Security →](02-network-security.md) · [← Back to README](../README.md)

</div>
