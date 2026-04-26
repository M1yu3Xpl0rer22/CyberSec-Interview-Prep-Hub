# 📱 Mobile, Cloud, Opinion, Programming & Random Q&A

> Mixed bag of topics — Mobile AppSec, Cloud, GRC, coding challenges, and opinion-based questions.

[← Back to README](../README.md) · [← Previous: SOC & IR](06-soc-ir-dfir.md)

---

## 📱 Mobile Application Pentesting

### Q1: Common risks in Mobile Applications?

**A:**
- **Insecure Data Storage** — Sensitive data in SharedPreferences, SQLite, plist files
- **Insecure Communication** — No SSL/TLS or weak implementation
- **Broken Authentication** — Weak session management, no biometric
- **Code Tampering** — Repackaged apps, reverse engineering
- **Insufficient Cryptography** — Hardcoded keys, weak algorithms
- **Client-Side Injection** — SQLi, XSS via WebViews

> Reference: **OWASP Mobile Top 10**

---

### Q2: How to detect if a device is jailbroken/rooted?

**A:**

**Android (Rooted):**
```java
// Check for su binary
File("/system/bin/su").exists()
File("/system/xbin/su").exists()
// Check for Magisk/SuperSU packages
// Check if can execute 'su' command
// Check for test-keys in build properties
```

**iOS (Jailbroken):**
```swift
// Check for Cydia/Sileo
FileManager.default.fileExists(atPath: "/Applications/Cydia.app")
// Check for writable system paths
// Check if can open cydia:// URL scheme
// Check for SSH daemon on port 22
```

> Attackers can bypass these checks — use multiple detection methods + server-side validation.

---

### Q3: Can SMS trigger SQLi on Android?

**A:** **Yes, theoretically.** If an app reads SMS content via a `BroadcastReceiver` and inserts it directly into a SQLite query without sanitization, an attacker could send an SMS containing SQL payload.

```
SMS content: '; DROP TABLE messages; --
```

**Conditions:** App must have SMS permission + parse SMS content into raw SQL queries (rare but possible in poorly coded apps).

---

### Q4: Tool to hook into iOS applications?

**A:** **Frida** — Dynamic instrumentation toolkit. Allows runtime hooking, function interception, and modification.
- Also: **Objection** (built on Frida), **Cycript** (older)
- Used for: SSL pinning bypass, method tracing, runtime analysis

---

### Q5: Protection for distributing iOS apps on App Store?

**A:** **FairPlay DRM** (Digital Rights Management) — Apple encrypts the IPA binary. Only devices authorized via the user's Apple ID can decrypt and run it.
- To reverse engineer: Need to decrypt first using tools like **Clutch**, **bfdecrypt**, or **frida-ios-dump**

---

### Q6: Obfuscators for Mobile Apps?

**A:**

| Platform | Obfuscator |
|---|---|
| **Android** | ProGuard, R8 (default), DexGuard (commercial) |
| **iOS** | Swift Obfuscator, iXGuard, Arxan |
| **Cross-platform** | Dotfuscator (.NET/Xamarin) |

> Obfuscation makes reverse engineering harder but **not impossible**.

---

### Q7: Secure data storage in mobile apps?

**A:**

| Platform | Recommended Storage |
|---|---|
| **Android** | EncryptedSharedPreferences, Android Keystore, SQLCipher |
| **iOS** | Keychain Services, Data Protection API (NSFileProtection) |

**Best Practices:**
- Never store sensitive data in plaintext
- Use platform-provided secure storage APIs
- Encrypt databases (SQLCipher)
- Don't log sensitive data
- Clear sensitive data from memory when done

---

### Q8: Reverse engineering an iOS app from App Store?

**A:**
```
1. Download IPA from App Store (via Apple Configurator or iMazing)
2. Decrypt the binary (FairPlay DRM) — use frida-ios-dump on jailbroken device
3. Extract IPA contents (unzip)
4. Analyze binary — class-dump (Obj-C headers), Hopper/IDA/Ghidra
5. Check Info.plist for URL schemes, permissions, ATS config
6. Search for hardcoded secrets (strings, API keys)
7. Dynamic analysis with Frida — hook methods, bypass checks
```

---

### Q9: Repackaged APK on new device — will it install?

**A:** **No, not by default.** The original APK is signed with the developer's private key. After modifying and repackaging:
- You must **re-sign** with a different key (your own)
- The new signature won't match the original
- **Google Play Protect** may flag it
- If the app uses **signature verification** at runtime, it will detect tampering

> Use `apktool` to decompile, modify, repackage, then `jarsigner`/`apksigner` to re-sign.

---

### Q10: ADB command to fetch APK from device?

**A:**
```bash
# List all packages
adb shell pm list packages

# Find APK path for a package
adb shell pm path com.example.app
# Output: package:/data/app/com.example.app-1/base.apk

# Pull the APK
adb pull /data/app/com.example.app-1/base.apk ./app.apk
```

---

### Q11: Can Android malware extract SQLite of another app?

**A:** **No, under normal circumstances.** Android sandboxes each app — apps cannot access other apps' data directories (`/data/data/com.other.app/`).

**Exceptions:**
- **Rooted device** — Root access bypasses sandbox, can read any app's data
- **Backup extraction** — If `android:allowBackup="true"` in AndroidManifest
- **Content Provider vulnerabilities** — Misconfigured exported providers
- **Shared User ID** — Apps signed with same certificate can share data

---

### Q12: Bypassing SSL Pinning?

**A:**

**Android:**
- **Frida + objection:** `objection explore --startup-script disable-ssl-pinning.js`
- **Custom Frida script** to hook `TrustManager`
- **Patch APK** — modify network security config to trust user CAs
- **Xposed Framework** — TrustMeAlready, SSLUnpinning modules

**iOS:**
- **Frida + objection:** `ios sslpinning disable`
- **SSL Kill Switch 2** (jailbroken device)
- **Custom Frida script** to hook `SecTrustEvaluate`

---

## ☁️ Cloud Security

### Q13: Common AWS S3 Misconfigurations?

**A:**
- **Public buckets** — ACL or policy allows `s3:GetObject` for `*` (everyone)
- **Public listing** — Bucket allows listing all objects
- **No encryption** — Data at rest not encrypted (SSE-S3, SSE-KMS)
- **No versioning** — Accidental deletes are permanent
- **No logging** — No CloudTrail or S3 access logs
- **Overly permissive IAM policies** — `s3:*` on `*` resources
- **Static website hosting** — Exposing internal files

**Tools:** AWS Config, ScoutSuite, Prowler, S3Scanner

---

## 💭 Opinion-Based / Scenario Questions

> No right or wrong — show your **reasoning and balanced thinking**.

### Q14: Open-source vs Proprietary?

**A:** *Balanced answer:*
- **Open-source** — Community audited, transparent, customizable, free. But may lack enterprise support.
- **Proprietary** — Dedicated support, polished UI, compliance certifications. But you trust the vendor blindly.
- **Best approach:** Use open-source where community is strong (Linux, Wireshark, Snort), proprietary where enterprise support matters (CrowdStrike, Splunk).

---

### Q15: Geo-blocking IP ranges — good idea?

**A:** *It depends:*
- ✅ **Yes if:** Your business only serves specific regions — blocks a huge attack surface
- ❌ **No if:** Attackers use VPNs/proxies to bypass, blocks legitimate users traveling, false sense of security
- **Best approach:** Geo-blocking as **one layer** alongside other controls, not as the sole defense

---

### Q16: Should you pay ransomware?

**A:** **Generally, no.**
- No guarantee data will be decrypted
- Funds criminal organizations and encourages more attacks
- May violate laws (paying sanctioned entities)
- Decryption tools may be available (nomoreransom.org)
- **However:** Some orgs with no backups and critical operations may have no choice — it's a business decision, not just technical

---

### Q17: How do you stay updated in security?

**A:**
- **News:** The Hacker News, BleepingComputer, Krebs on Security, Dark Reading
- **Podcasts:** Darknet Diaries, Security Now, CyberWire
- **Twitter/X:** Follow security researchers, CVE alerts
- **Communities:** Reddit r/netsec, Discord servers, local meetups
- **Practice:** TryHackMe, HackTheBox, CTFs
- **Certifications:** Continuous learning path
- **Vendor blogs:** Microsoft Security, Google TAG, CrowdStrike

---

### Q18: Insider Threats — how to detect?

**A:** Threats from employees, contractors, or partners with legitimate access.

**Detection:**
- **UEBA** (User & Entity Behavior Analytics) — baseline normal behavior, alert on anomalies
- **DLP** — Monitor for unusual data transfers
- **Access monitoring** — Alert on off-hours access, privilege abuse
- **Network monitoring** — Unusual outbound traffic volumes
- **HR signals** — Disgruntled employees, resignation notices

---

### Q19: iOS vs Android — which is more secure?

**A:** *Balanced answer:*
- **iOS:** Closed ecosystem, strict App Store review, consistent updates, hardware security (Secure Enclave). But a single exploit affects all devices.
- **Android:** Open source (auditable), more fragmented (delayed updates), larger attack surface (sideloading APKs). But Google Play Protect + monthly patches improving.
- **Neither is "secure"** — both have been exploited. Security depends on user behavior + patch management.

---

## 💻 Programming & Automation

### Q20: Fetch IP addresses from JSON file (Python)?

```python
import json
import re

with open('data.json', 'r') as f:
    data = json.load(f)

# If IPs are in a specific field
ips = [entry['ip'] for entry in data if 'ip' in entry]

# Or extract all IPs from entire file using regex
with open('data.json', 'r') as f:
    content = f.read()
ip_pattern = r'\b(?:\d{1,3}\.){3}\d{1,3}\b'
ips = re.findall(ip_pattern, content)
print(ips)
```

---

### Q21: Fetch valid email addresses from JSON (Python)?

```python
import json
import re

with open('data.json', 'r') as f:
    content = f.read()

email_pattern = r'[a-zA-Z0-9._]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}'
emails = re.findall(email_pattern, content)
print(emails)
```

---

### Q22: Regex patterns for common extractions?

```python
import re

# URL
r'https?://[^\s<>"\']+' 

# URL with query params
r'https?://[^\s<>"\']+\?[^\s<>"\']*'

# Email
r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}'

# IP Address
r'\b(?:\d{1,3}\.){3}\d{1,3}\b'

# Phone (10 digits)
r'\b\d{10}\b'
```

---

### Q23: Bash — replace all occurrences of a string?

```bash
# Using sed (in-place replacement)
sed -i 's/string_1/string_1_1/g' file.txt

# Explanation:
# s/  = substitute
# g   = global (all occurrences)
# -i  = in-place edit
```

---

### Q24: Extract payload from PCAP file (Python)?

```python
from scapy.all import rdpcap, Raw

packets = rdpcap('capture.pcap')

for pkt in packets:
    if pkt.haslayer(Raw):
        payload = pkt[Raw].load
        # Check for EXE/ELF magic bytes
        if payload[:2] == b'MZ':       # Windows EXE
            with open('extracted.exe', 'wb') as f:
                f.write(payload)
        elif payload[:4] == b'\x7fELF': # Linux ELF
            with open('extracted.elf', 'wb') as f:
                f.write(payload)
```

---

## 🎲 Random Questions

### Q25: Social media at workplace — OK?

**A:** *Balanced:*
- **Risk:** Social engineering vector, data leakage, productivity loss, credential phishing
- **LinkedIn** — Generally acceptable (professional), but oversharing org details is a risk
- **Instagram** — Higher risk (personal, can be used for social engineering recon)
- **Best practice:** Allow with awareness training, restrict on sensitive networks, monitor for data leakage

---

### Q26: Linux vs Windows — which is more secure?

**A:** *Neither is inherently more secure:*
- **Linux:** Open source (auditable), fewer malware targets, better permission model, no registry. But misconfigured Linux is just as vulnerable.
- **Windows:** Larger attack surface (market share = more targets), Active Directory complexity, more malware. But has improved significantly (Defender, Credential Guard, WDAC).
- **Security depends on:** Configuration, patching, user behavior, and hardening — not just the OS.

---

<div align="center">

**🎯 You've covered 160+ questions. Now go ace that interview!**

[← Previous: SOC & IR](06-soc-ir-dfir.md) · [← Back to README](../README.md)

</div>
