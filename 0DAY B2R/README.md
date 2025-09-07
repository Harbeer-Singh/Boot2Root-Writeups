# ✅ TryHackMe CTF Walkthrough: 0day

This repository contains a detailed walkthrough of the **0day CTF** I completed on TryHackMe, showcasing a full exploitation path from initial access to privilege escalation and flag capture.

---

## 🎯 Room Overview

The **0day CTF** is designed to simulate a real-world vulnerable machine exploitation scenario.  
It focuses on the following key areas:

- **Reconnaissance** – Discover open services and gather information about the target system.  
- **Vulnerability Identification** – Use automated tools and manual techniques to identify known vulnerabilities.  
- **Exploitation** – Leverage Metasploit and manual methods to exploit vulnerabilities and gain system access.  
- **Privilege Escalation** – Identify weaknesses in the system configuration or software versions to escalate privileges.  
- **Flag Capture** – Extract user and root flags to complete the challenge.

---

## 📚 Step-by-Step Walkthrough

1. Connect With the TryHackMe server using Command:  
   ```bash
   sudo openvpn Downloads/Ynos.ovpn
   ```

2. Confirm you can connect to the machine using the command:  
   ```bash
   ping <IP of the machine>
   ```

3. Start the nmap scan with:  
   ```bash
   nmap -sV -O -T5 -p- 10.10.<IP> -oN Nmap.results
   ```

4. Nmap results show that the apache server and ssh are open on this device.

5. And no directories on this machine.

6. So now we use Nikto with the command:  
   ```bash
   nikto -h 10.10.125.30
   ```

7. Nikto finds that the site appears to be vulnerable to the **shellshock vulnerability** [CVE-2014-6271 and CVE-2014-7169].

8. The best way to exploit this vulnerability is via **Metasploit**, so we will use Metasploit via:  
   ```bash
   msfconsole
   ```

9. We will type:  
   ```bash
   search shellshock
   ```  
   in the msfconsole and it will show that it is exploitable and has an excellent exploit, so we will attack it and we gain access to the terminal and user flag.

10. Then with enumeration, we find out that the kernel version of the terminal is too old and subsequently search for its exploits by running:  
    ```bash
    Linux ubuntu 3.13.0-32-generic exploit
    ```

11. We found an exploit on **ExploitDB**, downloaded the exploit which is in C language, and uploaded it onto the machine using Meterpreter.  
    Then we compile and run this file on the target computer.

12. When we run this file, we get admin access to this PC and find the root flag in the root folder.

---

## 🎯 Skills Gained

- Hands-on experience with **VPN connectivity and network reconnaissance**.  
- Mastered vulnerability scanning using **Nmap and Nikto**.  
- Practical exploitation of **Shellshock vulnerability** with **Metasploit**.  
- Real-world **privilege escalation** techniques via kernel exploits.  
- Flag capture and report preparation.

---

## ⚡ Final Outcome

- Successfully exploited **Shellshock vulnerability** using Metasploit.  
- Performed **privilege escalation** by compiling and running a public exploit for the old kernel version.  
- Retrieved both **user and root flags**.

---

## 🔗 Room Link

[0day – TryHackMe](https://tryhackme.com/room/0day)

---

## 👤 Author

**Harbeer-Singh**

