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

🛠️ Next Steps:

    Interact with the Python Code Editor. Test for:

        Arbitrary code execution

        Upload/execute payloads

        Sandbox escape (if limited)
