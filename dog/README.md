# 🐾 Penetration Test Report – HTB “Dog” Machine

**Target IP**: `10.10.11.58`  
  
**Tester**: *PRIME*

---

## 🔐 1. VPN Connection

Before interacting with the target machine, a VPN connection was established via the HTB platform to securely access the internal lab environment.

📸 **Screenshot 1**: *VPN connection confirmation*  

![VPN Connection](https://github.com/PrimeMurcia/htb/blob/main/dog/ss/ss1.png?raw=true)


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

📸 Screenshot 2 & 3: 

Nmap Scan 

![Nmap Scan](https://github.com/PrimeMurcia/htb/blob/main/dog/ss/SS2.png?raw=true)

Nmap Result

![Nmap Result](https://github.com/PrimeMurcia/htb/blob/main/dog/ss/ss3.png?raw=true)


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

```bash
./gitdumper.sh http://10.10.11.58/.git/ git-dump
```

This successfully downloaded the contents of the exposed .git directory for offline analysis.


📸 Screenshot 4: Git dump execution result

![Git dump execution result](https://github.com/PrimeMurcia/htb/blob/main/dog/ss/ss4.png?raw=true)


🔑 5. Credential Discovery – settings.php

After successfully dumping the exposed Git repository, I reviewed the contents for sensitive information. One file of particular interest was settings.php, which is part of a Backdrop CMS installation.

Within this file, hardcoded credentials for the MySQL database were discovered:

$database = 'mysql://root:BackDropJ2024DS2024@127.0.0.1/backdrop';

🧠 Breakdown:

    Username: root

    Password: BackDropJ2024DS2024

    Host: 127.0.0.1 (localhost)

    Database: backdrop

This information could allow an attacker to:

    Gain full access to the database (if MySQL is accessible remotely or through a web shell).

    Enumerate users, passwords, content, and sensitive configurations.

    Potentially escalate to full system compromise if RCE is available via SQL injection or file upload functionality.

🔒 Security Risk:

    Hardcoding database credentials in a Git repository is a high-severity vulnerability.

    Credentials should be stored in environment variables or secured configuration files that are not tracked by version control (e.g., using .gitignore).

Dog Files
![DOG Files](https://github.com/PrimeMurcia/htb/blob/main/dog/ss/ss5.png?raw=true)

Setting File
![DOG Files](https://github.com/PrimeMurcia/htb/blob/main/dog/ss/ss6.png?raw=true)
