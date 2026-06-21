**Goal** - Get access to target system and retrieve the flag

**Tool** - Official guide, my knowledge and AI

*(note: my first challenge)*

---

## Walkthrough

### 1) Connection Verification
First the challenge requires to check if the connection exists. For this I used **ping**:

`ping <target IP>`

![Connection Verification](Screenshot%202026-06-21%20074500.png)

> **NOTE:** After using that, I searched and found there are other ways to check if the connection exists or not. Other than ping's ICMP packet, **nmap** also has a feature to check the connection status using `nmap -sn <Target_IP>`.

### 2) Port Enumeration
We now have the connection checked. The next step is to look for open ports. I used the standard nmap scan for this:

`nmap <target IP>`

![Standard Nmap Scan](Screenshot%202026-06-21%20074703.png)

This scan tells us that port 23 running service telnet is open for receiving packets.

> **NOTE:** In my further research, I found a better nmap command: `nmap -sV -sC -p- <Target_IP>`
> Here, `-sV` is service version used to probe the exact software name and version, `-sC` is Default Scripts used to run built-in scripts to test for common vulnerabilities automatically, and `-p-` is used to scan all the ports, not just the top 1000.

![Aggressive Nmap Scan](Screenshot%202026-06-21%20074830.png)

### 3) Exploitation
We have the service figured out. The next step is to try exploiting this port using telnet. So I ran:

`telnet <target_IP>`

![Telnet Connection](Screenshot%202026-06-21%20074949.png)

I then tried to find some common mistakes made by administrators during the testing phase, before making the system live. I used login credentials which are generally left with blank passwords like root, admin, administrators, etc. Here, **root** worked and we got access to the target system.

### 4) Post-Exploitation
I then checked the directory I was dropped into using `pwd` (print working directory).

![PWD Command](Screenshot%202026-06-21%200749581.png)

I was dropped into `/root`. Next, I used `ls` to get a list of all the files. There, I found only one file named **flag.txt**. This was my target; I used `cat <file_name>` to read its content.

> **NOTE:** In further research, I found the method to close a telnet connection natively without just closing the terminal window (using `Ctrl + ]` then typing `quit`).

![Flag Captured](Screenshot%202026-06-21%20075045.png)

---

## Vulnerability Analysis
Here, the flaw was access account security, which was left unattended by the administrator from the testing phase until the system was made live. They also used Telnet, an outdated remote login software that does no encryption. The usage of SSH should have been prioritized, but the deployment focused more on ease of use, resulting in a critical risk exposure.
