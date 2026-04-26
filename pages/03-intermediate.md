# 🟡 Intermediate Level Questions & Answers

> Level up from basics. Focus on the **security aspect** when answering.

[← Back to README](../README.md) · [← Previous: Network Security](02-network-security.md)

---

### Q1: What is the Three-Way Handshake?

**A:** The process TCP uses to establish a connection:

```
Client  →  SYN         →  Server     (I want to connect)
Client  ←  SYN-ACK     ←  Server     (OK, I acknowledge)
Client  →  ACK         →  Server     (Connection established)
```

**3 packets total** are exchanged. After this, data transfer begins.

> **SYN Flood attack** exploits this by sending thousands of SYNs without completing the handshake, exhausting server resources.

---

### Q2: How many packets in a 3-way handshake?

**A:** **3 packets** — SYN, SYN-ACK, ACK. Connection teardown uses **4 packets** (FIN, ACK, FIN, ACK) — called a 4-way teardown.

---

### Q3: Explain Brute Force Attack. How to detect it?

**A:** Systematically trying every possible password/key combination until the correct one is found.

**Detection:**
- Multiple failed login attempts from same IP (Event ID 4625 on Windows)
- Account lockout triggers
- SIEM correlation rules for login threshold breaches
- Unusual login patterns (speed, time, geography)

**Variants:** Dictionary attack (common passwords), Credential stuffing (leaked credentials), Reverse brute force (one password, many usernames)

---

### Q4: How to prevent Brute Force?

**A:**
- **Account lockout** after N failed attempts (e.g., lock after 5 failures)
- **Rate limiting** — throttle login attempts
- **CAPTCHA** after failed attempts
- **MFA/2FA** — even if password is cracked, attacker needs second factor
- **Strong password policy** — minimum length, complexity
- **IP blocking** — block after repeated failures
- **Fail2ban** (Linux) — auto-bans offending IPs

---

### Q5: 2FA — How it protects? Can it be bypassed with Phishing?

**A:** **2FA** requires two verification factors: something you **know** (password) + something you **have** (OTP/phone) or **are** (biometric).

**Can it be bypassed?** Yes, through:
- **Real-time phishing proxies** (Evilginx2, Modlishka) — captures both password and OTP in real-time, relays to real site
- **SIM swapping** — attacker takes over phone number to receive SMS OTP
- **Social engineering** — tricking user to share OTP

> **Mitigation:** Hardware keys (YubiKey/FIDO2) are phishing-resistant because they verify the actual domain.

---

### Q6: SSL vs TLS?

**A:**

| Feature | SSL | TLS |
|---|---|---|
| **Full Name** | Secure Sockets Layer | Transport Layer Security |
| **Status** | ❌ Deprecated (SSL 3.0 in 2015) | ✅ Current standard |
| **Latest** | SSL 3.0 (vulnerable to POODLE) | TLS 1.3 (2018) |
| **Security** | Weaker, known vulnerabilities | Stronger, faster handshake |

> People still say "SSL" but actually mean **TLS**. TLS is the successor to SSL.

---

### Q7: How does SSL/TLS protect?

**A:** Provides three protections:
1. **Encryption** — Data encrypted in transit (AES)
2. **Authentication** — Server proves identity via certificate (asymmetric crypto)
3. **Integrity** — Data can't be tampered with (MAC/HMAC)

---

### Q8: SSL/TLS Certificate Exchange (Handshake)?

**A:** Simplified TLS 1.2 handshake:

```
1. Client Hello     → Supported cipher suites, TLS version, random number
2. Server Hello     ← Chosen cipher suite, server certificate, random number
3. Client verifies  → Checks certificate against trusted CAs
4. Key Exchange     → Client sends pre-master secret encrypted with server's public key
5. Both derive      → Session keys from pre-master secret + random numbers
6. Finished         → Encrypted communication begins (symmetric encryption)
```

> TLS 1.3 is faster — only **1 round trip** (1-RTT) instead of 2.

---

### Q9: DMZ vs Non-DMZ?

**A:**
- **DMZ** (Demilitarized Zone) — A network segment between the internal network and the internet. Hosts **public-facing services** (web servers, email servers, DNS).
- **Non-DMZ** — The internal/private network with sensitive systems.

```
Internet ←→ [Firewall] ←→ DMZ ←→ [Firewall] ←→ Internal Network
```

> If a DMZ server is compromised, the attacker still can't directly access the internal network.

---

### Q10: Metadata — What is it? What risk does it cause?

**A:** **Data about data** — hidden information embedded in files.

| File Type | Metadata Contains |
|---|---|
| Photos (EXIF) | GPS location, camera model, date/time |
| Documents | Author name, organization, edit history |
| Emails | IP addresses, mail servers, routing |
| PDFs | Creator software, author, timestamps |

**Risk:** Leaks sensitive info. **Tools:** ExifTool, FOCA, Metagoofil
**Example:** A leaked document reveals internal usernames and software versions → helps attacker in reconnaissance.

---

### Q11: TCP vs UDP?

| Feature | TCP | UDP |
|---|---|---|
| **Connection** | Connection-oriented (handshake) | Connectionless |
| **Reliability** | Guaranteed delivery, ordering | No guarantee |
| **Speed** | Slower (overhead) | Faster |
| **Use Cases** | HTTP, SSH, FTP, SMTP | DNS, VoIP, streaming, gaming |
| **Header Size** | 20 bytes | 8 bytes |

---

### Q12: DNS — How does resolution happen?

**A:** DNS translates domain names → IP addresses. **Port 53**, primarily **UDP** (TCP for zone transfers and large responses).

```
1. Browser checks local cache
2. Queries OS resolver cache
3. Queries configured DNS server (recursive resolver)
4. Resolver queries Root DNS server → gets TLD server
5. Queries TLD server (.com) → gets authoritative server
6. Queries Authoritative DNS → gets IP address
7. IP returned to browser, cached for future use
```

---

### Q13: What is DLP?

**A:** **Data Loss Prevention** — tools and policies that prevent sensitive data from leaving the organization.

- **Network DLP** — Monitors data in transit (email, web, FTP)
- **Endpoint DLP** — Monitors data on devices (USB, clipboard, print)
- **Cloud DLP** — Monitors cloud storage and SaaS apps

**Examples:** Symantec DLP, Microsoft Purview, Digital Guardian

---

### Q14: Data Exfiltration — Methods?

**A:** Unauthorized transfer of data out of an organization.

| Method | Description |
|---|---|
| **DNS Tunneling** | Encode data in DNS queries |
| **HTTP/S uploads** | Upload to cloud storage, paste sites |
| **Email** | Attach and send to external email |
| **USB/Physical** | Copy to removable media |
| **Steganography** | Hide data within images/files |
| **Encrypted channels** | Use VPN/SSH tunnels to exfil |
| **ICMP Tunneling** | Hide data in ping packets |

---

### Q15: How to detect Data Exfiltration?

**A:**
- **DLP tools** — Detect sensitive data leaving via email, web, USB
- **SIEM correlation** — Alert on large outbound transfers, unusual hours
- **DNS monitoring** — Detect DNS tunneling (long/unusual queries, high volume)
- **NetFlow analysis** — Spot abnormal data volume to external IPs
- **Endpoint monitoring** — Track USB usage, file copies
- **UEBA** — User & Entity Behavior Analytics for anomaly detection

---

### Q16: Common ports and services?

| Port | Service | Protocol | Port | Service | Protocol |
|---|---|---|---|---|---|
| 20/21 | FTP | TCP | 139/445 | SMB | TCP |
| 22 | SSH | TCP | 443 | HTTPS | TCP |
| 23 | Telnet | TCP | 993 | IMAPS | TCP |
| 25 | SMTP | TCP | 3306 | MySQL | TCP |
| 53 | DNS | UDP/TCP | 3389 | RDP | TCP |
| 67/68 | DHCP | UDP | 5432 | PostgreSQL | TCP |
| 80 | HTTP | TCP | 8080 | HTTP Alt | TCP |

> **If you see heavy traffic on port 22** — Possible SSH brute force or tunnel. Check source IP, frequency, success/failure. Block if malicious, alert IR.

---

### Q17: Firewall, Load Balancer, Proxy — In what order?

**A:** Recommended placement:

```
Internet → [Firewall] → [Load Balancer] → [Reverse Proxy] → [Web Servers]
```

- **Firewall first** — Block malicious traffic before it reaches anything
- **Load Balancer** — Distribute allowed traffic across servers
- **Reverse Proxy** — SSL termination, caching, additional filtering

---

### Q18: What info from a MAC address?

**A:** A MAC address (e.g., `00:1A:2B:3C:4D:5E`) reveals:
- **First 3 octets (OUI)** — Manufacturer/vendor (lookup on macvendors.com)
- **Last 3 octets** — Unique device identifier
- **Cannot determine:** Location, IP address, or user identity

> MAC addresses can be **spoofed** easily, so don't rely on them for security.

---

### Q19: What port does PING use?

**A:** PING uses **ICMP** (Internet Control Message Protocol) — it **doesn't use a port**. ICMP operates at **Layer 3** (Network layer), while ports are a Layer 4 concept (TCP/UDP).

---

### Q20: Linux — File vs Directory?

**A:**
```bash
ls -l          # First character: - = file, d = directory, l = symlink
file filename  # Tells you the file type
stat filename  # Detailed info including type
test -f file && echo "file" || echo "not file"
test -d dir && echo "directory"
```

---

### Q21: Linux Redirections?

**A:**
```bash
>    # Redirect stdout to file (overwrite)
>>   # Redirect stdout to file (append)
2>   # Redirect stderr to file
2>&1 # Redirect stderr to stdout
<    # Read input from file
/dev/null  # Discard output

# Examples
ls > output.txt           # Save output
ls 2> errors.txt          # Save errors only
ls > all.txt 2>&1         # Save both output and errors
```

---

### Q22: Pipes? Named Pipes?

**A:**
- **Pipe (`|`)** — Sends output of one command as input to another: `cat file.txt | grep "error"`
- **Named Pipe (FIFO)** — A special file that acts as a pipe between processes, persists on filesystem

```bash
mkfifo mypipe              # Create named pipe
echo "data" > mypipe &     # Writer
cat < mypipe               # Reader gets "data"
```

> Named pipes are used in IPC (Inter-Process Communication) and can be abused for persistence in attacks.

---

<div align="center">

[← Previous: Network Security](02-network-security.md) · [Next: Pentesting →](04-pentesting.md) · [← Back to README](../README.md)

</div>
