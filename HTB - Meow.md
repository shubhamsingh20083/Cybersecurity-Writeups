
**Goal** - Get access to target system and retrieve the flag
**Tool** - Official guide, my knowledge and AI
(note: my first challenge)

---

**Walkthrough:**

1) Connection Verification

First the challenge requires to check if the connection exists. For this I uses **ping** - 

`ping <target IP>`

![[Screenshot 2026-06-21 074500.png]]

(NOTE: after using that I searched and found there are other ways to check if the connection exists or not. Other than ping's ICMP packet, **nmap** also has a feature to check the connection status - `nmap -sn <Target_IP>`)


2) Port Enumeration

We now have the connection checked. The next step is to look for open ports. I used the standard nmap scan for this - 

`nmap <target IP>`

![[Screenshot 2026-06-21 074703.png]]

This scan tells us that port 23 running service telnet is open for receiving packets.

(NOTE: In my further research I found a better nmap command - `nmap -sV -sC -p- <Target_IP>`
here the -sV is service version used to probe the exact software name and version, -sC is Default Dcripts which is used to run built-in scripts to test for common vulnerabilities automatically. -p- is used to scan all the port not just he top 1000 ports.)

![[Screenshot 2026-06-21 074830.png]]

3) Exploitation

We have the service figured out. The next step is to try exploiting this port using telnet. So I ran - 

`telnet <target_IP>`

![[Screenshot 2026-06-21 074949.png]]

I then tried to find some common mistakes made by administrators during the testing face, before making the system live. I used login credentials which are generally left with blank password like root, admin, administrators, etc. Here **root** worked :) and we got access to the target system. 

4) Post-Exploitation

I then checked the directory I was dropped into using `pwd` (print working directory).


![[Screenshot 2026-06-21 074958 1.png]]

I was dropped into /root. Next I used `ls` to get a list of all the files. There I founded only one file named **flag.txt**. This was my target, I used `cat <file_name>` to read it's content.  

(NOTE: In further research I founded the method to close telnet connection without just closing the terminal.)

![[Screenshot 2026-06-21 075045.png]]

___

**Vulnerability Analysis:** Here the flaw was access account security which was left unattended by the administrator since the testing phase until the system was made live. They also used Telnet, an outdated remote login software which does no encryption. Usage of SSH should have been prioritized but I guess they focused more on ease and made a mistake. 
