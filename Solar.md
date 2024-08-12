![image](https://github.com/user-attachments/assets/d58ba67a-d34b-44ca-be2a-2bf2582b2409)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration

### Port scan:

I initiated the enumeration process with a quick `rustscan`, which revealed five open ports on the target host:
```
rustscan -a 10.10.11.16 -- -A -T4 -vv -oN solar_nmap
```
![image](https://github.com/user-attachments/assets/05dfa1ac-2971-4a3c-8d81-4a344af072ba)


```rs
PORT     STATE SERVICE       REASON          VERSION
80/tcp   open  http          syn-ack ttl 127 nginx 1.24.0
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://solarlab.htb/
|_http-server-header: nginx/1.24.0
135/tcp  open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp  open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds? syn-ack ttl 127
6791/tcp open  http          syn-ack ttl 127 nginx 1.24.0
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://report.solarlab.htb:6791/
|_http-server-header: nginx/1.24.0
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
No OS matches for host
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=8/9%OT=80%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=66B5ACE1%P=x86_64-pc-linux-gnu)
SEQ(SP=100%GCD=1%ISR=109%TI=I%II=I%SS=S%TS=U)
OPS(O1=M53CNW8NNS%O2=M53CNW8NNS%O3=M53CNW8%O4=M53CNW8NNS%O5=M53CNW8NNS%O6=M53CNNS)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)
ECN(R=Y%DF=Y%TG=80%W=FFFF%O=M53CNW8NNS%CC=N%Q=)
T1(R=Y%DF=Y%TG=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=N)
U1(R=N)
IE(R=Y%DFI=N%TG=80%CD=Z)

Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=256 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-08-09T05:44:29
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: 2s
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 64857/tcp): CLEAN (Timeout)
|   Check 2 (port 57530/tcp): CLEAN (Timeout)
|   Check 3 (port 48381/udp): CLEAN (Timeout)
|   Check 4 (port 63724/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked

TRACEROUTE (using port 135/tcp)
HOP RTT       ADDRESS
1   162.62 ms 10.10.14.1
2   146.00 ms 10.10.11.16
```

#### Identified Domains:

- **Port 80**: The scan revealed the domain `http://solarlab.htb/`. I added this domain to my hosts file.
- **Port 6791**: Another domain, `http://report.solarlab.htb:6791/`, was also discovered on port 6791 and added to my hosts file for further investigation.

### Web enumeration:

The main website at `http://solarlab.htb/` appears to be a generic webpage with a mention of an "Unhackable Instant Messenger!"

![image](https://github.com/user-attachments/assets/abaefe0e-0bdd-461a-a75b-35fb5f81272e)

On the domain `http://report.solarlab.htb:6791/`, I found a login page.

![image](https://github.com/user-attachments/assets/9d57cdbd-37f4-4bfa-9170-a4d47ea1de2c)

I performed sub-directory enumeration on both domains using `dirsearch`, but didn't discover anything noteworthy:😕

```
dirsearch -u http://solarlab.htb -x 404,403 --crawl
```

![image](https://github.com/user-attachments/assets/0edd9b9f-341f-4d75-bb44-9f454cc81e8d)

![image](https://github.com/user-attachments/assets/636aaf58-6d09-43e7-a6de-33a7ddcb9ec4)


### SMB enumeration:

After exhausting my web enumeration options, I turned to SMB enumeration since port 445 was open. I used `smbclientng` to connect to the SMB shares.

- Null User Authentication: My initial attempt with null user authentication failed:

```
smbclientng -u "" -p '' --target "10.10.11.16"
```

![image](https://github.com/user-attachments/assets/695def08-91a9-43b6-aa07-2d6e2bdb8c92)

- Guest User Authentication: I tried again with the "guest" user and a null password. This time, I was successfully authenticated and discovered a share named `documents`:

```
smbclientng -u 'guest' -p '' --target "10.10.11.16"
```

![image](https://github.com/user-attachments/assets/f5ac9d16-3374-46b1-9d48-8391d12af565)

- Document Files: The `documents` share contained multiple files, which I downloaded for further analysis:

![image](https://github.com/user-attachments/assets/f6745ae4-3da0-4936-97f4-13274604ef78)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### File Analysis:

- Metadata Analysis: I examined the metadata of each document and identified several usernames associated with these files:

![image](https://github.com/user-attachments/assets/043e71a9-1029-40ca-9480-522ebe7c5dfb)

![image](https://github.com/user-attachments/assets/70c09f62-eaf9-4fbe-afdc-1615a1848c66)

![image](https://github.com/user-attachments/assets/2f2d53b7-db7e-43e2-bdbd-0717795c587f)

![image](https://github.com/user-attachments/assets/f6461418-2bc7-4b39-911d-ffd15bdd0c00)

The usernames I found were:

```
Paul Squillace
Katy Brown
Gayle.Rennie
FRYATT, Susanne
Alison Melville
Jackie
Paul Serban
```

- Content Analysis:

  Word Documents: Upon reviewing the contents of the three Word documents, I found that they didn't contain anything useful for my enumeration:

![image](https://github.com/user-attachments/assets/90009e6d-1f48-4479-aa31-a8605af29ebe)
![image](https://github.com/user-attachments/assets/5ada26d8-99f6-4cbf-ba4f-50abce137d54)
![image](https://github.com/user-attachments/assets/bb04efe3-c597-4fca-ba6f-5f0e1f8883e2)

- Excel Sheet: The Excel sheet, however, was more revealing. It contained a list of passwords and security question answers, which could be valuable for further exploration:

![image](https://github.com/user-attachments/assets/b897e660-fe4b-4d98-8f2e-7795a9de0b9e)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access

### ReportHub access:

- Login Attempts:
    Initially, I tried logging into the `report.solarlab.htb` portal using the credentials from the Excel sheet. Most attempts resulted in a "**User not found**" error:

![image](https://github.com/user-attachments/assets/6972acd0-946e-4798-91ce-63e4fb22be74)

- Username Format Identification:
    After testing various usernames, I observed a different error, "**User Authentication error,**" when attempting to log in with the usernames `AlexanderK` and `ClaudiaS`:

```
AlexanderK
ClaudiaS
```

![image](https://github.com/user-attachments/assets/74c5ff65-0791-4ad6-af6c-91f97b4e620b)

The corresponding full names are:

![image](https://github.com/user-attachments/assets/54e87c65-a6b0-4720-b38f-9190098619c4)

```
Alexander Knight
Claudia Springer
```

This helped me deduce the username format used in the application: `Firstname + Last Initial`. I then created a list of valid usernames.

- Brute Forcing with Valid Usernames:
    I performed brute force attempts using Burp Intruder with the list of valid usernames and the passwords from the Excel sheet. After filtering out the invalid users:

![image](https://github.com/user-attachments/assets/0ec28cfb-e434-41a9-863d-0d8f89941a78)

- Successful Login:
    Finally, after identifying three valid usernames, I successfully logged in as user BlakeB:

![image](https://github.com/user-attachments/assets/e84bd051-e8f4-4a7d-bd17-383803c5f3c0)

![image](https://github.com/user-attachments/assets/a3354bb2-c98d-4766-8a15-4e3f31d6100a)

### Shell:

- ReportHub Portal Overview:
    After logging into the ReportHub portal, I observed four different options to generate reports:

![image](https://github.com/user-attachments/assets/a6fef854-f940-4bef-a5b1-e36c3937f27e)

All four options had similar functionality for report generation:

![image](https://github.com/user-attachments/assets/f6b82480-a97b-4417-8d8d-4888a350f969)

- Exploitation Research:
While searching for potential vulnerabilities in ReportHub, I found an RCE vulnerability, `CVE-2023-33733`, related to ReportLab. There was a proof-of-concept (PoC) available on [GitHub](https://github.com/c53elyas/CVE-2023-33733/blob/master/code-injection-poc/poc.py).

- Executing the PoC:
Following the GitHub PoC, I captured a request in Burp Suite by filling out the `travel_request` form with arbitrary details and uploading an image:

![image](https://github.com/user-attachments/assets/d9f838db-f594-4280-9d6d-2ccdff1c6bfc)

I injected the PoC code into the `travel_request` and used a `ping` command to test the vulnerability. I confirmed that the vulnerability was exploitable as I received ICMP responses on my Kali host:

![image](https://github.com/user-attachments/assets/4b73926c-0a09-490f-b187-6ff198a93680)

![image](https://github.com/user-attachments/assets/cc256d66-2ffe-4ff6-9b2d-e4c5e9e0d1b4)

- Reverse Shell Execution:
I crafted a Base64-encoded PowerShell payload for a reverse shell and sent the request while listening with netcat. Shortly after, I received a reverse shell as user `solarlab\blake`:🙂

![image](https://github.com/user-attachments/assets/bb0d7fb3-0b8a-4022-a45d-67edf89dccc9)

I also found the user flag on Blake's desktop.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Priv Esc

### Exploring Internal Services via Port Forwarding:

- Initial Enumeration:
After gaining access as `Blake`, I searched for ways to escalate privileges. My user did not have any special privileges assigned. During my exploration, I discovered a database file named `users.db`:

![image](https://github.com/user-attachments/assets/4f53f254-1e39-4ce4-aa78-d01746168206)

Unfortunately, these users didn't exist on the host, so I continued searching for other potential escalation methods.

![image](https://github.com/user-attachments/assets/ec79c498-6219-4033-b5c8-1846e0410122)

- Internal Service Discovery:
I ran WinPEAS but didn't find anything directly useful. However, I did notice several internal services running on localhost. Among them, I observed a user named `openfire`, which led me to suspect the presence of Openfire, an XMPP server.

I learned that Openfire typically operates on ports 9090 and 9091. Using the `netstat` command, I confirmed that these ports were active:

![image](https://github.com/user-attachments/assets/3fbb839e-588f-4dc5-a276-22b6bafccbe1)

![image](https://github.com/user-attachments/assets/a0db2ae3-e7d5-47a2-973f-460003876e05)

- **Port Forwarding via Ligolo-ng:**
    To access these internal services, I set up port forwarding using `ligolo-ng`. Here's the process I followed:

```
sudo ip tuntap add user kali mode tun ligolo #to add ligolo interface

sudo ip link set ligolo up #to start ligolo interface

./proxy -selfcert #to start ligolo proxy with self certificate

.\agent.exe -connect kali_ip:11601 -ignore-cert #to connect back to our proxy from target host

session > start

sudo ip route add 240.0.0.1/32 dev ligolo #to add new route of target host
```

![image](https://github.com/user-attachments/assets/147c5557-4662-494e-98dc-6bdbe7b32621)

### Openfire exploit:

After successfully setting up port forwarding and accessing the Openfire service at `http://240.0.0.1:9090`, I found that it was running version `4.7.4`. I identified an authentication bypass vulnerability (CVE-2023-32315) and used a PoC exploit available on [github](https://github.com/miko550/CVE-2023-32315).

![image](https://github.com/user-attachments/assets/bf44b9e3-0f52-4c2a-a9c2-73a212a2c086)

Using this exploit, I extracted credentials:

![image](https://github.com/user-attachments/assets/595b47f4-f223-4b02-bca7-4c387950146f)

With these credentials, I logged into the Openfire portal and uploaded a malicious `jar` plugin, as instructed by the PoC. I accessed the malicious plugin via the management tools:

![image](https://github.com/user-attachments/assets/b0c292c3-9ad7-447f-b7c0-69e2694c6501)

- Reverse Shell Setup:

![image](https://github.com/user-attachments/assets/6259fd1a-7db7-43f9-a047-dccd33fe4238)

Once I gained shell access on the application, I executed a PowerShell command to establish a reverse shell connection back to my Kali host. I used the [Villain C2 framework](https://github.com/t3l3machus/Villain) to manage shells, connections, and to obtain a proper `conptyshell` on the Windows host:

![image](https://github.com/user-attachments/assets/e1ec1db4-8c9b-467e-8704-68c18f2a033d)

Despite successfully obtaining a shell, I still lacked the necessary privileges to access the root flag: 😕

![image](https://github.com/user-attachments/assets/6ee463b4-51a4-4f98-aec4-dbe8d4c2233e)

### Root flag:

- Exploring Openfire Directory:

After gaining access with the Openfire account, I explored the `C:\Program Files\Openfire` directory and discovered a file named `embedded-db`, which contained an `openfire.script` file:

![image](https://github.com/user-attachments/assets/18be28f8-d7f6-46c8-bba8-c487a2775620)

Within this script, I found credentials for the `admin` user, but they were hashed:

![image](https://github.com/user-attachments/assets/783497fa-bd88-4e71-b4c9-8366a3cc05d3)

- Decrypting the Admin Password:
I searched for ways to decrypt Openfire password hashes and found a [POC on GitHub](https://github.com/c0rdis/openfire_decrypt) written in Java. According to the POC, I needed the `Blowfish key` to decrypt the hash, which I found in the same script file:

![image](https://github.com/user-attachments/assets/5171939e-d817-4ba2-aa99-f7db64e875d6)

![image](https://github.com/user-attachments/assets/b447605d-6123-464b-b04c-9915191c8843)

By utilizing the POC script, the password hash, and the Blowfish key, I successfully decrypted the administrator password:🙂

![image](https://github.com/user-attachments/assets/46039283-be24-4d05-9407-cdeff75c5970)

With the decrypted credentials, I used `psexec` to log in as the administrator and finally obtained the root flag:(pwn3d!🎉)

![image](https://github.com/user-attachments/assets/3dc0dc2c-41b1-46a9-8e21-21b570ccfa9e)
