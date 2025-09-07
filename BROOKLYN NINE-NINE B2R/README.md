
# Brooklyn Nine-Nine CTF Walkthrough – TryHackMe

This is a detailed walkthrough of how I solved the **Brooklyn Nine-Nine CTF** on **TryHackMe**. This room focuses on basic penetration testing skills, including network scanning, exploiting open services, brute-forcing SSH, and privilege escalation to gain root access. It’s a great exercise for beginners to practice enumeration, exploitation, and post-exploitation techniques in a controlled environment.

## 🟠 Room Overview

The Brooklyn Nine-Nine CTF introduces learners to typical penetration testing steps:

- Connecting to the target machine via VPN
- Performing network enumeration and service discovery
- Exploiting common vulnerabilities such as anonymous FTP access
- Using brute-force techniques for SSH login
- Privilege escalation using file misconfigurations

By the end of this room, you’ll understand how to enumerate a machine, exploit it, and escalate privileges—all using essential Linux and security tools.

---

## ✅ Steps I Followed

### 1️⃣ Connect to the TryHackMe VPN Server
I began by establishing a secure connection with the TryHackMe network.

```
$ sudo openvpn Downloads/Ynos.ovpn
```

This allowed my machine to communicate with the CTF environment safely.

---

### 2️⃣ Confirm Connectivity
Once connected, I verified that the target machine was reachable by sending ICMP packets using the `ping` command.

```
$ ping (Ip of the machine)
```

This confirmed that the machine was live and responding.

---

### 3️⃣ Scan for Open Ports with Nmap
Next, I scanned the machine for open ports and services using `nmap`. This is a crucial step to understand what services are running and where potential attack vectors may lie.

```
$nmap -sV -O -T5 -p- (Ip of the machine) -oN nmap.results
```

- `-sV`: Service version detection  
- `-O`: OS detection  
- `-T5`: Aggressive scan timing  
- `-p-`: Scan all ports  
- `-oN`: Output results to a file

---

### 4️⃣ Identify Open FTP Port
The scan results showed that the **FTP service** was open, which is a known entry point if misconfigured.

---

### 5️⃣ Exploit the FTP Service
I connected to the FTP server using the following command:

```
$ftp (Ip of the machine)
```

---

### 6️⃣ Login with Anonymous Credentials
Many FTP servers allow anonymous login. I tried this using:

```
$anonymous
```

---

### 7️⃣ List Files on the FTP Server
Once connected, I listed available files using:

```
$ ls
```

---

### 8️⃣ Download the Available File
I found a file that could help in further exploitation. I downloaded it using:

```
$get (filename)
```

---

### 9️⃣ Exit the FTP Server
After downloading the required file, I exited the server using:

```
$bye
```

---

### 🔑 SSH Brute Force Using Hydra
The next step was to brute-force the SSH service using `hydra`, a powerful password cracking tool.

```
$hydra -l jake -P /opt/rockyou.txt -V  -f ssh://(Ip of the machine) -o hydra.results
```

- `-l jake`: Username to brute-force  
- `-P /opt/rockyou.txt`: Password list  
- `-V`: Verbose output  
- `-f`: Stop after the first valid credential is found  
- `-o`: Output results to a file

---

### 🔑 Get SSH Credentials
After running `hydra`, it provided the valid **username** and **password**.

---

### 🔑 Connect to the Machine via SSH
With the credentials, I logged in:

```
$ ssh jake@(Ip of the machine)
```

At this point, **Jake** was compromised!

---

### 📂 Traverse and Find the User Flag
I navigated through the file system until I located the `user.txt` file. Reading it gave me the **User Flag**.

---

### 🟠 Privilege Escalation – Gain Root Access

#### Step 1: Exploit Misconfiguration in `/etc/shadow`
I used the `less` command with `sudo` rights:

```
$ sudo less /etc/shadow
```

This file contains password hashes and should be protected—but misconfigurations can allow privilege escalation.

#### Step 2: Execute a Shell from Less
Inside `less`, I typed:

```
!
```

and pressed Enter.

#### Step 3: Gain Root Access
This dropped me into a root shell! Now I had complete control over the machine.

---

### 📂 Capture the Root Flag

With root access, I navigated to the `root.txt` file and accessed it to retrieve the **Root Flag**.

---

## ✅ Final Thoughts

This room is a perfect introduction to penetration testing. Key takeaways:

✔ Always perform network scanning before launching attacks  
✔ Misconfigured FTP servers are a common and easily exploitable vulnerability  
✔ Brute-force tools like Hydra can be devastating if weak credentials are used  
✔ Misconfigured permissions, especially in system files like `/etc/shadow`, are a major privilege escalation vector  
✔ Post-exploitation steps like traversing directories and capturing flags are essential in CTFs

The Brooklyn Nine-Nine CTF taught me how to approach enumeration, exploitation, and privilege escalation methodically while reinforcing the importance of securing services.

---

## 📂 Files Used

- `Ynos.ovpn`: VPN configuration file  
- `nmap.results`: Scan results  
- `hydra.results`: SSH brute-force results  
- `rockyou.txt`: Common password list

---

This was a fun and educational challenge that helped me sharpen my penetration testing skills. I'm excited to apply these learnings in more advanced scenarios!

