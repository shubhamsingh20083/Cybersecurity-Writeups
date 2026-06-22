
**Goal** - Exploiting FTP oversight and retrieving the flag
**Tool** **Used** - Official guide, my knowledge and AI

---

## Walkthrough

### 1) Connection Verification
First the challenge requires to check if the connection exists. For this I used **ping**:

`ping <target IP>`

![Ping Output](Screenshot%202026-06-21%20193935.png)

### 2) Port Enumeration
We now have the connection checked. The next step is to look for open ports. I used the nmap scan for this:

`nmap -sV <target IP>`

![Nmap Scan](Screenshot%202026-06-21%20194200.png)

This scan tells us that port 21 running service ftp is open. FTP is a standard file managing protocol that was later replaced by safer/encrypted methods like FTPS (File Transfer Protocol Secure) and SFTP (Secure File Transfer Protocol). These both function differently, with FTPS behaving like FTP by utilizing dual ports, and SFTP using a single SSH tunnel to send and receive packets.

### 3) Exploitation
We have the service figured out. The next step is to try exploiting this port using ftp. So I ran:

`ftp -a <target_IP>`

![FTP Anonymous Login](Screenshot%202026-06-21%20194239.png)

Here I used `-a` to do an **anonymous** login, which by default is a credential login that requires no password and gives us access to the system just like any other authorized user, granting me access to the files.

> **NOTE:** After using this, I researched and found that ftp has help features called with the syntax `ftp -?`, and an in-system syntax to get a list of commands available to manage your files using `help`.

![FTP Help Syntax](Screenshot%202026-06-21%20194214.png)
![FTP Help Menu](Screenshot%202026-06-21%20194250.png)

### 4) Post-Exploitation
I then used the command `ls` to get the list of files, followed by `get <filename>` to download the file needed into the same directory I was in before I initiated the ftp connection.

![Directory List](Screenshot%202026-06-21%20194302.png)

Here the file named *flag.txt* was required.

![File Download 1](Screenshot%202026-06-21%20194356.png)
![File Download 2](Screenshot%202026-06-21%20194415.png)
![Flag Captured](Screenshot%202026-06-21%20194452.png)

---

## Vulnerability Analysis

Here, the flaw was using FTP and failing to configure it to deny anonymous access. Someone sniffing the network can easily use software like Wireshark to read the packets being sent, as FTP is not encrypted (just like Telnet). The usage of SFTP or FTPS should have been prioritized over administrative ease of use.