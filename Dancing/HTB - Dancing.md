**Goal** - Capture the Flag/exploit misconfigured SMB share
**Tool** **Used** - Official guide, my knowledge and AI *(nmap, ping, smbclient)*

---

## Walkthrough

### 1) Connection Verification
First the challenge requires to check if the connection exists. For this I used **ping**:

`ping <target IP>`

![Connection Verification](Screenshot%202026-06-25%20095017.png)


### 2) Port Enumeration
We now have the connection checked. The next step is to look for open ports. I used the nmap scan for this:

`nmap -sV <target IP>`

![Nmap Scan](Screenshot%202026-06-25%20095228.png)

This scan tells us that there are several open ports. The OS is windows. I searched and found that in windows by default certain ports are assigned to set SMB(server message block) running. SMB is used to transfer files, printer and communicate across a network, built in windows. It is used using *smbclient*. So I researched and found that port 139 functions as a legacy alias that runs SMB inside the older NetBIOS while port 445 is a modern implementation of SMB directly over TCP.

> **NOTE:** Port 5985 handles Windows Remote Management used to manage the machine remotely using PowerShell. Port 135 runs something called Microsoft Remote Procedure Calling that allows windows processes to coordinate across a network.

All of these default services makes Port 445 the primary high-value target for investigation.

### 3) Exploitation
We have the service figured out. The next step is to try exploiting this port using **smbclient**. So I ran:

`smbclient`

![SMB Client Commands](Screenshot%202026-06-25%20095241.png)

This gave us a list of commands that can be run using smbclient.

From the list I used `smbclient -L <target_IP>`, this gave us the list of shares available.
Shares can be thought of as folders created as per SMB.

![SMB Shares List](Screenshot%202026-06-25%20095346.png)

The list showed a share named `WorkShares`.

![Workshares Connect](Screenshot%202026-06-25%20100018.png)

Using *smbclient* I got inside the `WorkShares` share using `anonymous` login, no password (blank).I then typed `help` to view the commands available to *smbclient*.

> **NOTE:** Also the way to enter SMB utility is different than Telnet or FTP, well at least in the syntax.Here we enter a share as `smbclient \\\\<target_IP>\\share_name`.

### 4) Post-Exploitation
I then used the command `ls` to get the list of files followed by moving into directory using `cd` and downloading the .txt files using `get`.

![List Files](Screenshot%202026-06-25%20100103.png)
![Change Directory](Screenshot%202026-06-25%20100120%202.png)
![List User Files](Screenshot%202026-06-25%20100130%201.png)
![Download File](Screenshot%202026-06-25%20100147%201.png)
![Flag File Details](Screenshot%202026-06-25%20100237%201.png)


Here the file named *flag.txt* was required.

---

## Vulnerability Analysis

I think the root cause here is that the administrator was once again in a hurry. When they created the `WorkShares` folder to share files, they weren't able to set the anonymous login to false, or maybe they just forgot to do it before the attack happened.

Because of this simple mistake, anyone on the network could just log in with a blank password and steal the files without needing a real account.