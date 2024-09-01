![image](https://github.com/user-attachments/assets/ce9978e8-fa90-4562-a06d-16960213b454)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration:

I started by scanning for open ports and services using `rustscan` on the target environment. This scan revealed multiple open ports, providing insights into the available services.

![image](https://github.com/user-attachments/assets/fceef1ab-40ab-4b7c-8c42-d6da15e465c8)

```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Microsoft DNS 6.1.7601 (1DB15D39) (Windows Server 2008 R2 SP1)
| dns-nsid: 
|_  bind.version: Microsoft DNS 6.1.7601 (1DB15D39)
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-08-31 07:50:43Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: cascade.local, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: cascade.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49154/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49155/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49157/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49158/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49165/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|phone|specialized
Running (JUST GUESSING): Microsoft Windows 8|Phone|7|2008|8.1|Vista (92%)
OS CPE: cpe:/o:microsoft:windows_8 cpe:/o:microsoft:windows cpe:/o:microsoft:windows_7 cpe:/o:microsoft:windows_server_2008:r2 cpe:/o:microsoft:windows_8.1 cpe:/o:microsoft:windows_vista::- cpe:/o:microsoft:windows_vista::sp1
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows 8.1 Update 1 (92%), Microsoft Windows Phone 7.5 or 8.0 (92%), Microsoft Windows Embedded Standard 7 (91%), Microsoft Windows 7 or Windows Server 2008 R2 (89%), Microsoft Windows Server 2008 R2 (89%), Microsoft Windows Server 2008 R2 or Windows 8.1 (89%), Microsoft Windows Server 2008 R2 SP1 or Windows 8 (89%), Microsoft Windows 7 (89%), Microsoft Windows 7 Professional or Windows 8 (89%), Microsoft Windows 7 SP1 or Windows Server 2008 R2 (89%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=8/31%OT=53%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=66D2CBBA%P=x86_64-pc-linux-gnu)
SEQ(SP=105%GCD=1%ISR=109%TI=I%II=I%SS=O%TS=7)
SEQ(SP=105%GCD=1%ISR=109%TI=I%II=I%SS=S%TS=7)
OPS(O1=M53CNW8ST11%O2=M53CNW8ST11%O3=M53CNW8NNT11%O4=M53CNW8ST11%O5=M53CNW8ST11%O6=M53CST11)
WIN(W1=2000%W2=2000%W3=2000%W4=2000%W5=2000%W6=2000)
ECN(R=Y%DF=Y%TG=80%W=2000%O=M53CNW8NNS%CC=N%Q=)
T1(R=Y%DF=Y%TG=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=N)
U1(R=N)
IE(R=Y%DFI=N%TG=80%CD=Z)

Uptime guess: 0.010 days (since Sat Aug 31 03:37:55 2024)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=261 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: CASC-DC1; OS: Windows; CPE: cpe:/o:microsoft:windows_server_2008:r2:sp1, cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-08-31T07:51:47
|_  start_date: 2024-08-31T07:38:25
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 51409/tcp): CLEAN (Timeout)
|   Check 2 (port 47234/tcp): CLEAN (Timeout)
|   Check 3 (port 10882/udp): CLEAN (Timeout)
|   Check 4 (port 58957/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled and required
|_clock-skew: 1s

TRACEROUTE (using port 139/tcp)
HOP RTT       ADDRESS
1   222.13 ms 10.10.14.1
2   222.17 ms 10.10.10.182
```

From the port scan results, I observed a domain `cascade.local`. To facilitate further enumeration and potential exploitation, I added this domain to my hosts configuration file to ensure proper name resolution

## Kerbrute:

Given that port 88 (Kerberos) is open, I proceeded with Kerberos enumeration using `kerbrute`. This tool helps in identifying valid usernames in the network by leveraging the Kerberos protocol. I used a common set of usernames for this purpose:

```
kerbrute userenum --dc cascade.local -d cascade.local /usr/share/SecLists/Usernames/cirt-default-usernames.txt
```

Despite trying a list of usernames, `kerbrute` was able to identify only one valid user in the network: `Administrator`.

![image](https://github.com/user-attachments/assets/61f9dde0-7374-4bef-a00e-bf17728f0934)

## RPC Enumeration

Seeing that RPC is available and responding, I tried to enumerate information using NULL authentication, which indeed worked:

```
rpcclient -U '' -N cascade.local
```

Using RPC, I queried for domain users and groups, which revealed a significant amount of information:

![image](https://github.com/user-attachments/assets/460b7378-9f19-4958-ab78-54430071a604)

From these results, I compiled a list of valid usernames found in the domain. These usernames can now be leveraged for further enumeration or attacks, such as password spraying, brute force.

## LDAP enumeration:

For the confirmation purpose, I again checked the domain users using the `ldapsearch` & observed that we have total, 15 users exccluding the machine account:

```
ldapsearch -x -H ldap://cascade.local -b 'dc=cascade,dc=local' 'objectClass=person' | grep  "sAMAccountName:"
```

![image](https://github.com/user-attachments/assets/3a02fa5d-ac7d-4fe2-8f7f-d088af743346)

## SMB Enumeration:

Using the list of valid usernames gathered from RPC enumeration, I proceeded with SMB enumeration. My first step was to check the password policy using `netexec` to determine if there were any restrictions on password attempts, such as account lockout thresholds.

```
nxc smb cascade.local -u "" -p "" --pass-pol
```

![image](https://github.com/user-attachments/assets/7669950e-2a35-4f81-84b3-660475a79f55)

The results revealed no account lockout threshold, indicating that it would be possible to perform password spraying or brute-force attacks without risking account lockouts. I attempted to use the `user:user` credential combination for authentication, but this was unsuccessful:

![image](https://github.com/user-attachments/assets/7dedd5d8-65ef-4bfe-b76b-b554d9ce94f5)

I then attempted to authenticate as a NULL user and an anonymous user to access SMB file shares, but neither of these attempts was successful:

![image](https://github.com/user-attachments/assets/bc5a34af-b0b3-42f7-8242-f41b036f5580)

## AS-REP Roasting:

With a list of valid usernames, I attempted an AS-REP roasting attack to extract Kerberos tickets for users with the `UF_DONT_REQUIRE_PREAUTH` attribute set. This attack can be effective when such users exist, as it allows for offline brute-forcing of the extracted ticket hashes.

I used the following command with Impacket's `GetNPUsers` script:

```
impacket-GetNPUsers -dc-ip 10.10.10.182  -request cascade.local/ -format hashcat -usersfile users.txt -no-pass
```

![image](https://github.com/user-attachments/assets/5e7c7d4f-59e8-4c4e-8c8a-c61bd967d754)

Unfortunately, the AS-REP roasting attack failed. None of the users in the cascade.local domain had the `UF_DONT_REQUIRE_PREAUTH` attribute set, which is necessary for this type of attack.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Initial Access:

After various failed attempts, I revisited the LDAP enumeration results to find any overlooked information. I noticed an attribute called `cascadeLegacyPwd` associated with the user `Ryan Thompson`. The value appeared to be a base64-encoded password.

![image](https://github.com/user-attachments/assets/040f05c2-1a54-4ae1-8433-e7f91e20a0cb)

I decoded the cascadeLegacyPwd attribute using base64 decoding, which revealed the plaintext credentials:

![image](https://github.com/user-attachments/assets/ad765b63-c572-4b5e-8050-ba9511fb6910)

I tried to login with this credential but that didn't worked:

![image](https://github.com/user-attachments/assets/95449952-cdba-4d43-a392-b6fb2c454f00)

The decoded credentials didn't work for an interactive login, so I used a password spraying technique with `netexec` to find which services they could access. It turned out that the credentials worked for SMB access under the username `r.thompson`.

![image](https://github.com/user-attachments/assets/39bcb2f9-ab06-455f-ae98-0071e3bc6602)

Using `smbmap`, I identified accessible SMB shares. The `Data` share seemed interesting:

```
smbmap -u 'r.thompson' -p 'rY4n5eva' -H cascade.local
```

![image](https://github.com/user-attachments/assets/b90e76c8-b836-4c5d-9e4d-61540772455a)

To explore the `Data` share, I initially used `smbclientng`, but it didn't work as expected, so I switched to `smbclient`:

```
smbclient -U 'cascade/r.thompson' //cascade.local/Data
```

![image](https://github.com/user-attachments/assets/d60f0378-cdb2-4382-ac02-27e446d7a228)

I started downloading all files from the share, but found access was limited to the `IT` folder. Using `smbclient`, I recursively downloaded files from the `IT` directory.

```
recurse ON     #to download everything recursively
prompt OFF     #to off the prompt
mget *         #to download everything
```

![image](https://github.com/user-attachments/assets/7546e07d-9477-4f14-89b4-f027e41e6340)

## Analyzing the Files

In the `IT/Email Archive folder`, the file `Meeting_Notes_June_2018.html` mentioned a temporary admin account:

```
Username is TempAdmin (password is the same as the normal admin account password)
```

![image](https://github.com/user-attachments/assets/ed667cb7-12f3-4db9-bdfe-a7637d2e2821)

In `IT/Temp/s.smith`, a file named `VNC Install.reg` contained a VNC password in hex format.

![image](https://github.com/user-attachments/assets/714f9785-b0a4-4590-af12-2cf81fea23cb)

## Decrypting the VNC Password

Using the decryption method found [here](https://tenaka.gitbook.io/pentesting/useful-commands/password-cracking/vnc-decrypt-password), I successfully decrypted the VNC password:

```
echo -n 6bcf2a4b6e5aca0f | xxd -r -p | openssl enc -des-cbc --nopad --nosalt -K e84ad660c4721ae0 -iv 0000000000000000 -d | hexdump -C
```

![image](https://github.com/user-attachments/assets/9634742a-202f-4636-9158-ef429ff7644f)

### Successful Login as s.smith

After another password spray attempt, I found that the decrypted password worked for `s.smith`:

![image](https://github.com/user-attachments/assets/2a0b0776-bbe4-4334-af86-378dba783a51)

Using these credentials, I logged in as `s.smith` and successfully retrieved the user flag: 🙂

![image](https://github.com/user-attachments/assets/5919fa68-cdb3-468b-a42d-e73b690b6a23)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Root.txt

After obtaining the user flag, I began searching for methods to escalate my privileges. I started by examining my privileges using the following command, which revealed no special privileges assigned:

```
whoami /all
```

![image](https://github.com/user-attachments/assets/b55d0588-25b1-45d0-9945-d68a5e36001f)

Next, I checked the group memberships and noticed that the s.smith user belonged to two unique groups: `Audit Share` and `IT`:

```
net user s.smith
```

![image](https://github.com/user-attachments/assets/894d66ce-7d12-4e5a-b2aa-4d7b0e3553ea)

## s.smith --> arksvc

To gather more information, I used the `net` command to list the details of these groups. The `Audit Share` group had an interesting comment hinting at access to shared resources, but `s.smith` was the only member:

![image](https://github.com/user-attachments/assets/0b20c775-b3ee-4f3f-b031-ebea72aeee0f)

However, direct access to the shared directories from the host was not possible:

![image](https://github.com/user-attachments/assets/246d356c-559c-4fef-9a98-892bbc753416)

Instead, using `smbclient`, I accessed the `Audit` share and found several files and directories:

![image](https://github.com/user-attachments/assets/b93946b6-02d0-4d70-b5d4-13ada21760d1)

I downloaded an interesting file named `Audit.db` from the DB directory:

![image](https://github.com/user-attachments/assets/1ea15d8e-b9da-49fe-b677-13a79081655b)

Using sqlitebrowser to inspect `Audit.db`, I found four tables. One of the tables, Ldap, contained a base64-encoded password associated with the user `ArkSvc`:

![image](https://github.com/user-attachments/assets/54b7ada8-0f5b-4763-b683-a3a52bafe953)

![image](https://github.com/user-attachments/assets/f73ae23a-d961-487a-8a5f-e691913fc83a)

Attempting to decode this base64 string resulted in a non-standard encoding:

![image](https://github.com/user-attachments/assets/335e51a4-92f7-41bf-980a-4e5ad7104726)

## Extracting Encryption Keys

I found a batch file named `RunAudit.bat` in the same share, indicating the use of the `CascAudit.exe` and `CascCrypto.dll` files. These files suggested custom encryption was being used:

![image](https://github.com/user-attachments/assets/54996e9a-1daa-4c9c-88f5-033445be64f4)

After downloading both the executable and DLL files, I used `dnSpy` to decompile them. The decompiled code revealed the decryption key and the IV:

![image](https://github.com/user-attachments/assets/a7d30c60-5380-4c1e-a09d-d4725e798f8c)

The decryption process involved base64 decoding followed by AES-128 decryption using the extracted IV and key:

![image](https://github.com/user-attachments/assets/d3634a2b-f515-40a4-9cd6-a81aaa8a13d1)

I replicated these decryption steps using `CyberChef`, successfully revealing the plaintext password:

![image](https://github.com/user-attachments/assets/4c3833c7-1806-4b56-90c9-1433fd8c0b0b)

Using the decrypted password, I logged in as the `ArkSvc` user:

![image](https://github.com/user-attachments/assets/0c2b57bc-3127-4272-83fa-39bb642406c0)

## ArkSvc -> Administrator:

After logging in as `ArkSvc`, I discovered this account belonged to the `AD Recycle Bin` group:

![image](https://github.com/user-attachments/assets/38c055b4-3b65-4138-a3b3-9cfbf0e2315c)

Following a guide on [HackTricks](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/privileged-groups-and-token-privileges#a-d-recycle-bin), I learned that this membership allows reading deleted Active Directory objects, which can include sensitive information:

```
Membership in this group allows for the reading of deleted Active Directory objects, which can reveal sensitive information:
```

![image](https://github.com/user-attachments/assets/eb9e1420-26e6-4f66-8a8f-5563cbcaf5c9)

Using a PowerShell command from the blog, I retrieved multiple deleted items, including details of the `TempAdmin` account referenced earlier in the HTML file:

![image](https://github.com/user-attachments/assets/036e74bc-d19c-4d73-bb25-507188efe54c)

Decoding the credentials associated with TempAdmin and using them with the Administrator account finally granted me access as an administrator. Using this elevated access, I navigated to the Administrator's desktop and retrieved the root flag, achieving full system compromise:(pwn3d!🎉)

![image](https://github.com/user-attachments/assets/857c61d1-2614-4130-8abe-e85941d41ffe)



