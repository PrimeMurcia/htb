# 🐾 Penetration Test Report – HTB “Dog” Machine

**Target IP**: `10.10.11.58`  
  
**Tester**: *Prime*

---

## 🔐 1. VPN Connection

Before interacting with the target machine, a VPN connection was established via the HTB platform to securely access the internal lab environment.

📸 **Screenshot 1**: *VPN connection confirmation*  
`(Insert Screenshot 1 here)`

---

## 🔍 2. Initial Scanning & Information Gathering

A detailed Nmap scan was executed to enumerate open ports, services, and potential vulnerabilities.

### 🔧 Command Used:
```bash
nmap -sC -sV -A -T4 -Pn 10.10.11.58 -oN dog.txt
```

🧠 Explanation:

    -sC: Default scripts

    -sV: Service/version detection

    -A: Aggressive scan (OS detection, version detection, script scanning, and traceroute)

    -T4: Faster execution

    -Pn: No ping

    -oN: Output to dog.txt

📸 Screenshot 2 & 3: Nmap Scan and Results

💡 3. Discovery – Exposed Git Repository

The Nmap scan revealed that the .git directory was publicly accessible via HTTP.

    ⚠️ Security Risk: This misconfiguration allows an attacker to download the entire Git repository and potentially access sensitive files, credentials, or code history.

🌐 Discovery URL:

http://10.10.11.58/.git/

📦 4. Git Dumping

Used GitTools from InternetWache to dump the exposed repository.
🔧 Tool Used:

GitTools – InternetWache
💻 Command Executed:

./gitdumper.sh http://10.10.11.58/.git/ git-dump

This successfully downloaded the contents of the exposed .git directory for offline analysis.

📸 Screenshot 4: Git dump execution result
(Insert Screenshot 4 here)
