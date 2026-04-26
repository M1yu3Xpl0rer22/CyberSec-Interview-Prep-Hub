# 🌐 Network Security Questions & Answers

> Networks are intrinsic to any security setup. These questions are **initiators** — be prepared for deep follow-ups.

[← Back to README](../README.md) · [← Previous: Basic](01-basic-cybersecurity.md)

---

### Q1: What is traceroute and how do you use it?

**A:** A network diagnostic tool that shows the **path (hops)** packets take from source to destination, displaying each router and latency.

```bash
# Linux/Mac
traceroute google.com

# Windows
tracert google.com
```

**Use cases:** Identifying where packets are dropping, finding network bottlenecks, detecting rerouting/MITM.

---

### Q2: What is SSH? What port does it use?

**A:** **Secure Shell** — an encrypted protocol for remote access to systems. Runs on **port 22** (TCP).

- Replaces insecure Telnet (port 23, plaintext)
- Uses asymmetric encryption for auth, symmetric for session
- Supports key-based auth (more secure than passwords)
- Can tunnel other protocols (SSH tunneling / port forwarding)

---

### Q3: Can you SSH from Windows?

**A:** Yes.
- **Windows 10+** has built-in OpenSSH client (`ssh user@host` in PowerShell/CMD)
- **PuTTY** — popular third-party SSH client for Windows
- **Windows Terminal** — supports SSH natively
- **WSL** — full Linux SSH client available

---

### Q4: Why is DNS Monitoring Important?

**A:** DNS is used in nearly every network activity, making it a goldmine for security monitoring:
- **Detect malware C2 communication** — malware uses DNS to call home
- **Identify DNS tunneling / exfiltration** — data hidden in DNS queries
- **Catch phishing** — spot newly registered suspicious domains
- **Detect DGA** (Domain Generation Algorithm) — randomized domains used by botnets
- **Track shadow IT** — unauthorized services accessed by employees

---

### Q5: DNS Communication — Which port?

**A:** **Port 53** — Uses **UDP** for standard queries (fast, small packets) and **TCP** for zone transfers and responses larger than 512 bytes. DNS over HTTPS (DoH) uses port **443**, DNS over TLS (DoT) uses port **853**.

---

### Q6: What is a VPN?

**A:** **Virtual Private Network** — creates an encrypted tunnel between your device and a VPN server, masking your IP and encrypting all traffic.

- **Protocols:** OpenVPN, WireGuard, IPSec/IKEv2, L2TP
- **Use cases:** Secure remote access, bypass geo-restrictions, privacy on public WiFi
- **Corporate VPN:** Connects remote employees to internal network securely

---

### Q7: What is a Proxy?

**A:** An intermediary server between the client and the destination. The proxy makes requests **on behalf of** the client.
- Hides client IP from the destination server
- Can cache content, filter URLs, log traffic
- **Does NOT encrypt** traffic by default (unlike VPN)

---

### Q8: VPN vs Proxy?

| Feature | VPN | Proxy |
|---|---|---|
| **Encryption** | ✅ All traffic encrypted | ❌ Usually no encryption |
| **Scope** | OS-level (all apps) | App-level (browser only, usually) |
| **Speed** | Slightly slower (encryption overhead) | Faster (no encryption) |
| **Anonymity** | Higher | Lower |
| **Use Case** | Security + privacy | Bypass filters, caching |

---

### Q9: Forward Proxy vs Reverse Proxy?

**A:**
- **Forward Proxy** — Sits in front of **clients**. Client → Proxy → Internet. Hides client identity. (e.g., Squid, corporate proxy)
- **Reverse Proxy** — Sits in front of **servers**. Internet → Proxy → Server. Hides server identity. (e.g., Nginx, Cloudflare, HAProxy)

```
Forward:  Client → [Proxy] → Internet → Server
Reverse:  Client → Internet → [Proxy] → Server
```

---

### Q10: What is a Load Balancer?

**A:** Distributes incoming network traffic across multiple servers to ensure no single server is overwhelmed.

- **Algorithms:** Round Robin, Least Connections, IP Hash, Weighted
- **Types:** Layer 4 (TCP/UDP) and Layer 7 (HTTP/application)
- **Examples:** HAProxy, Nginx, AWS ALB/NLB, F5
- **Benefits:** High availability, scalability, fault tolerance

---

### Q11: What is a CDN?

**A:** **Content Delivery Network** — A geographically distributed network of servers that cache and deliver content from the nearest location to the user.
- **Reduces latency** — content served from nearest edge server
- **DDoS protection** — absorbs traffic spikes
- **Examples:** Cloudflare, Akamai, AWS CloudFront, Fastly

---

### Q12: Explain Man-in-the-Middle (MITM) attack?

**A:** Attacker secretly intercepts and potentially alters communication between two parties who believe they're communicating directly.

**Methods:** ARP spoofing, DNS spoofing, Wi-Fi eavesdropping, SSL stripping, rogue access points

**Prevention:** HTTPS/TLS, certificate pinning, HSTS, VPN, avoid public WiFi, verify certificates

---

### Q13: Does HTTPS/SSL protect from MITM?

**A:** **Mostly yes, but not always.**
- ✅ **Protects:** Encrypts data in transit, verifies server identity via certificate
- ❌ **Doesn't protect if:** Attacker installs a rogue CA certificate, SSL stripping is used (downgrade to HTTP), user ignores certificate warnings
- **HSTS** (HTTP Strict Transport Security) helps prevent SSL stripping

---

### Q14: IDS vs IPS?

| Feature | IDS | IPS |
|---|---|---|
| **Full Name** | Intrusion Detection System | Intrusion Prevention System |
| **Action** | Detects and **alerts** | Detects and **blocks** |
| **Position** | Passive (monitors copy of traffic) | Inline (sits in traffic flow) |
| **Risk** | Misses nothing but can't stop attacks | Can block legitimate traffic (false positive) |
| **Examples** | Snort (IDS mode), Zeek | Snort (IPS mode), Suricata, Palo Alto |

---

### Q15: OSI Layers in Networking?

```
Layer 7 — Application     (HTTP, FTP, DNS, SMTP)
Layer 6 — Presentation    (SSL/TLS, JPEG, encryption)
Layer 5 — Session         (NetBIOS, RPC, session management)
Layer 4 — Transport       (TCP, UDP — ports)
Layer 3 — Network         (IP, ICMP, routing)
Layer 2 — Data Link       (MAC addresses, Ethernet, switches)
Layer 1 — Physical        (Cables, signals, hubs)
```

> Mnemonic: **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way

---

### Q16: TCP/IP vs OSI?

| OSI (7 layers) | TCP/IP (4 layers) |
|---|---|
| Application + Presentation + Session | **Application** |
| Transport | **Transport** |
| Network | **Internet** |
| Data Link + Physical | **Network Access** |

TCP/IP is the **practical model** used in real networks. OSI is the **theoretical reference** model.

---

### Q17: Filtered ports vs Closed ports on firewall?

**A:** **Filtered is preferred.**
- **Closed port** — responds with RST packet, confirming the host exists (gives info to attacker)
- **Filtered port** — drops packets silently, no response (attacker doesn't know if host exists)

> Filtered ports = less information leakage = better security posture.

---

### Q18: What is a firewall? Types?

**A:** A network security device that monitors and controls traffic based on security rules.

| Type | Description |
|---|---|
| **Packet Filtering** | Checks headers (IP, port) — fast but basic |
| **Stateful Inspection** | Tracks connection state — smarter than packet filtering |
| **Proxy/Application** | Inspects application-layer data — deep inspection |
| **NGFW** (Next-Gen) | Combines stateful + DPI + IPS + app awareness |
| **WAF** | Specifically protects web applications (Layer 7) |

---

### Q19: How to bypass a firewall or IDS?

**A:** Common techniques (from a pentesting perspective):
- **Fragmentation** — Split packets to evade signature detection
- **Encryption** — Encrypted tunnels (SSH, VPN) bypass content inspection
- **Protocol tunneling** — DNS tunneling, ICMP tunneling, HTTP tunneling
- **Evasion encoding** — Unicode, hex encoding to evade pattern matching
- **Slow scanning** — Spread scans over time to avoid rate-based detection
- **Port hopping** — Use allowed ports (80, 443) for C2 traffic

---

### Q20: What is a Fragmentation attack?

**A:** Attacker sends maliciously crafted fragmented packets that exploit how systems reassemble them.
- **Teardrop attack** — overlapping fragments crash the target
- **IDS evasion** — split malicious payload across fragments so no single fragment triggers a signature
- **Tiny fragment attack** — fragments too small to contain full header info, bypass firewall rules

---

### Q21: Fragmentation as DoS? How to prevent?

**A:** Flooding a target with fragmented packets forces it to allocate memory for reassembly, exhausting resources.

**Prevention:**
- Set minimum fragment size thresholds
- Drop fragmented packets at firewall (if not needed)
- Use IPS with fragment reassembly before inspection
- Rate-limit fragmented traffic

---

### Q22: Other devices for network boundaries?

**A:** Beyond firewalls:
- **IDS/IPS** — Detect/prevent intrusions
- **Proxy servers** — Control web access
- **NAC** (Network Access Control) — Authenticate devices before network access
- **VPN Gateways** — Secure remote access
- **DMZ** — Isolated network segment for public-facing services
- **Network Segmentation / VLANs** — Isolate sensitive zones
- **Zero Trust Architecture** — Never trust, always verify

---

### Q23: What is a Honeypot?

**A:** A **decoy system** intentionally made vulnerable to attract attackers, allowing defenders to:
- Study attacker techniques and tools
- Detect intrusions early
- Divert attackers from real assets
- Collect threat intelligence

**Types:** Low-interaction (simulates services) vs High-interaction (full OS/app)
**Tools:** Cowrie (SSH), Dionaea, HoneyD, T-Pot

---

### Q24: HIDS vs NIDS?

| Feature | HIDS | NIDS |
|---|---|---|
| **Full Name** | Host-based IDS | Network-based IDS |
| **Monitors** | Single host (files, logs, processes) | Network traffic |
| **Placement** | Installed on endpoint | Positioned on network tap/span |
| **Detects** | File changes, rootkits, local attacks | Network scans, MITM, traffic anomalies |
| **Examples** | OSSEC, Wazuh, Tripwire | Snort, Suricata, Zeek |

---

### Q25: False Negative vs False Positive — which is worse?

**A:** **False Negative is worse.**
- **False Positive** — Alert fires but no actual threat → wastes analyst time
- **False Negative** — Real threat but NO alert → attack goes undetected

> A false negative means you **missed a real attack**. That's always worse than investigating a false alarm.

---

### Q26: DDoS vs DoS?

| Feature | DoS | DDoS |
|---|---|---|
| **Source** | Single machine | Multiple machines (botnet) |
| **Scale** | Limited | Massive |
| **Detection** | Easier (single IP) | Harder (distributed IPs) |
| **Mitigation** | Block source IP | Rate limiting, CDN, traffic scrubbing |

**Types:** Volumetric (UDP flood), Protocol (SYN flood), Application Layer (HTTP flood)

---

### Q27: IP Subnetting?

**A:** Dividing a network into smaller sub-networks for better management and security.

```
Example: 192.168.1.0/24
- /24 = 255.255.255.0 = 256 IPs (254 usable)
- /25 = 255.255.255.128 = 128 IPs per subnet
- /26 = 255.255.255.192 = 64 IPs per subnet
```

**Security benefit:** Isolate departments, limit broadcast domains, apply different firewall rules per subnet.

---

### Q28: NAT (Network Address Translation)?

**A:** Translates **private IP addresses** to **public IP addresses** (and vice versa) at the router/firewall level.

- **Why?** IPv4 address shortage — many devices share one public IP
- **Types:** Static NAT (1:1), Dynamic NAT (pool), PAT/NAT Overload (many:1 using ports)
- **Security benefit:** Hides internal network structure from external view

---

### Q29: Port Forwarding?

**A:** Redirecting traffic from a specific port on the router/firewall to a specific internal machine and port.

```
External request → Router:8080 → Internal Server:80
```

**Use cases:** Hosting a web server behind NAT, remote access to internal services, SSH access to internal machine.

---

### Q30: What is VLAN?

**A:** **Virtual Local Area Network** — logically segments a physical network into separate broadcast domains.
- Devices on different VLANs **can't communicate** without a router (Layer 3)
- **Security:** Isolates sensitive systems (e.g., HR, Finance, Servers on separate VLANs)
- **VLAN hopping** — an attack to bypass VLAN separation (mitigate with proper switch config)

---

### Q31: Non-repudiation principle?

**A:** **Non-repudiation** — guarantees that a sender **cannot deny** having sent a message. A signed message came from the owner of the key that signed it.
- Achieved through **digital signatures** (private key signs, public key verifies)
- Provides proof of origin and integrity
- Used in legal documents, financial transactions, email signing (S/MIME, PGP)

---

### Q32: ARP Poisoning?

**A:** Attacker sends fake ARP (Address Resolution Protocol) replies to associate their MAC address with the IP of a legitimate device (like the gateway).

**Impact:** All traffic meant for the gateway flows through the attacker → enables **MITM**, sniffing, session hijacking.

**Detection & Prevention:**
- Static ARP entries for critical devices
- Dynamic ARP Inspection (DAI) on switches
- ARP monitoring tools (arpwatch, XArp)
- Network segmentation

---

<div align="center">

[← Previous: Basic](01-basic-cybersecurity.md) · [Next: Intermediate →](03-intermediate.md) · [← Back to README](../README.md)

</div>
