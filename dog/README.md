# 🐾 Penetration Test Report – HTB “Dog” Machine

**Target IP**: `10.10.11.58`  
**Date**: *(Insert Date)*  
**Tester**: *(Your Name or Alias)*

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
