![image](https://github.com/user-attachments/assets/6ff6b011-47b2-4c65-b574-dae53492b87a)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration:

I began the enumeration process by running a quick `rustscan`, which revealed multiple open ports on the target host:

```
rustscan -a 10.10.11.152 -- -A -T4 -vv -oN time_nmap
```

![image](https://github.com/user-attachments/assets/5aaab285-2718-4503-8f5b-2c716d942d35)

```
PORT      STATE SERVICE           REASON          VERSION
53/tcp    open  domain            syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec      syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-08-29 13:21:25Z)
135/tcp   open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn       syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap              syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?     syn-ack ttl 127
464/tcp   open  kpasswd5?         syn-ack ttl 127
593/tcp   open  ncacn_http        syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ldapssl?          syn-ack ttl 127
3268/tcp  open  ldap              syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
3269/tcp  open  globalcatLDAPssl? syn-ack ttl 127
5986/tcp  open  ssl/http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| ssl-cert: Subject: commonName=dc01.timelapse.htb
| Issuer: commonName=dc01.timelapse.htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-10-25T14:05:29
| Not valid after:  2022-10-25T14:25:29
| MD5:   e233:a199:4504:0859:013f:b9c5:e4f6:91c3
| SHA-1: 5861:acf7:76b8:703f:d01e:e25d:fc7c:9952:a447:7652
| -----BEGIN CERTIFICATE-----
| MIIDCjCCAfKgAwIBAgIQLRY/feXALoZCPZtUeyiC4DANBgkqhkiG9w0BAQsFADAd
| MRswGQYDVQQDDBJkYzAxLnRpbWVsYXBzZS5odGIwHhcNMjExMDI1MTQwNTI5WhcN
| MjIxMDI1MTQyNTI5WjAdMRswGQYDVQQDDBJkYzAxLnRpbWVsYXBzZS5odGIwggEi
| MA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDJdoIQMYt47skzf17SI7M8jubO
| rD6sHg8yZw0YXKumOd5zofcSBPHfC1d/jtcHjGSsc5dQQ66qnlwdlOvifNW/KcaX
| LqNmzjhwL49UGUw0MAMPAyi1hcYP6LG0dkU84zNuoNMprMpzya3+aU1u7YpQ6Dui
| AzNKPa+6zJzPSMkg/TlUuSN4LjnSgIV6xKBc1qhVYDEyTUsHZUgkIYtN0+zvwpU5
| isiwyp9M4RYZbxe0xecW39hfTvec++94VYkH4uO+ITtpmZ5OVvWOCpqagznTSXTg
| FFuSYQTSjqYDwxPXHTK+/GAlq3uUWQYGdNeVMEZt+8EIEmyL4i4ToPkqjPF1AgMB
| AAGjRjBEMA4GA1UdDwEB/wQEAwIFoDATBgNVHSUEDDAKBggrBgEFBQcDATAdBgNV
| HQ4EFgQUZ6PTTN1pEmDFD6YXfQ1tfTnXde0wDQYJKoZIhvcNAQELBQADggEBAL2Y
| /57FBUBLqUKZKp+P0vtbUAD0+J7bg4m/1tAHcN6Cf89KwRSkRLdq++RWaQk9CKIU
| 4g3M3stTWCnMf1CgXax+WeuTpzGmITLeVA6L8I2FaIgNdFVQGIG1nAn1UpYueR/H
| NTIVjMPA93XR1JLsW601WV6eUI/q7t6e52sAADECjsnG1p37NjNbmTwHabrUVjBK
| 6Luol+v2QtqP6nY4DRH+XSk6xDaxjfwd5qN7DvSpdoz09+2ffrFuQkxxs6Pp8bQE
| 5GJ+aSfE+xua2vpYyyGxO0Or1J2YA1CXMijise2tp+m9JBQ1wJ2suUS2wGv1Tvyh
| lrrndm32+d0YeP/wb8E=
|_-----END CERTIFICATE-----
|_ssl-date: 2024-08-29T13:23:03+00:00; +8h00m01s from scanner time.
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
| tls-alpn: 
|_  http/1.1
9389/tcp  open  mc-nmf            syn-ack ttl 127 .NET Message Framing
49667/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49673/tcp open  ncacn_http        syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49693/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019 (88%)
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows Server 2019 (88%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=8/29%OT=53%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=66D005BC%P=x86_64-pc-linux-gnu)
SEQ(SP=FA%GCD=1%ISR=10F%TI=I%II=I%SS=S%TS=U)
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
TCP Sequence Prediction: Difficulty=250 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: 8h00m00s, deviation: 0s, median: 8h00m00s
| smb2-time: 
|   date: 2024-08-29T13:22:27
|_  start_date: N/A
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 32357/tcp): CLEAN (Timeout)
|   Check 2 (port 29423/tcp): CLEAN (Timeout)
|   Check 3 (port 22941/udp): CLEAN (Timeout)
|   Check 4 (port 47078/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked

TRACEROUTE (using port 49667/tcp)
HOP RTT       ADDRESS
1   228.09 ms 10.10.14.1
2   228.17 ms 10.10.11.152
```

From the scan results, I identified the domain controller name `dc01.timelapse.htb` and the domain `timelapse.htb`. I added these entries to my host configuration file.

## Kerbrute:

Since port 88 is open and running the Kerberos protocol, I proceeded with the enumeration using a tool called `kerbrute`, along with a list of default usernames:

```
kerbrute userenum --dc dc01.timelapse.htb -d timelapse.htb /usr/share/SecLists/Usernames/cirt-default-usernames.txt
```

Out of 828 usernames, I identified two valid usernames: `Administrator` and `Guest`.

![image](https://github.com/user-attachments/assets/e0a8f4d8-ce4e-4d76-b9dc-7a059e2ec533)

## RPC enumeration:

Next, I attempted RPC enumeration using the tool `rpcclient`, but found that NULL authentication was denied:

```
rpcclient -U '' -N 10.10.11.152
```

![image](https://github.com/user-attachments/assets/4483c920-aa81-4148-81fe-bc07977597df)

## LDAP enumeration:

Next, I attempted LDAP enumeration. However, I found that this also required authentication:

```
ldapsearch -x -H ldap://timelapse.htb -s base namingcontexts

ldapsearch -x -H ldap://timelapse.htb -b "dc=timelapse,dc=htb"
```

![image](https://github.com/user-attachments/assets/49507710-0666-4191-85c6-aaa84de479c3)

![image](https://github.com/user-attachments/assets/30a1ff79-dc2f-4298-81b3-db50eda8c9c6)

## SMB Enumeration:

Next, I performed SMB enumeration. I started with NULL authentication, but it didn't work. I then tried using the `anonymous` user, which successfully listed the shares:

```
smbclientng -u '' -p '' --target "10.10.11.152"

smbclientng -u 'anonymous' -p '' --target "10.10.11.152"
```
![image](https://github.com/user-attachments/assets/e87ecfed-2563-4488-a2ea-5fabb3b19f75)

I noticed an uncommon shared folder named `Shares`. Upon enumerating this folder, I found multiple files inside. I downloaded all of them locally for further analysis:

![image](https://github.com/user-attachments/assets/5e772e5b-e04d-4715-b56f-1895c341accb)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Initial access:

After downloading all the files, I began inspecting them one by one. The office documents contained generic information about the LAPS solution, and their metadata did not reveal anything useful:

![image](https://github.com/user-attachments/assets/92e54c7e-3d26-43d2-aef2-2fec943d419f)

When I attempted to unzip the compressed folder, I discovered it was password-protected. To crack the password, I converted it into a John the Ripper format using `zip2john`:

![image](https://github.com/user-attachments/assets/6232009e-7695-465c-8780-c5cfcbb963a1)

```
zip2john winrm_backup.zip > john_zip
```

After conversion, I was able to quickly crack the password:

![image](https://github.com/user-attachments/assets/a08dfa50-9e64-44f3-8da7-eb59c5b0370f)

Using the cracked password, I unzipped the file and found a PFX certificate file inside:

![image](https://github.com/user-attachments/assets/6513a8e2-72f3-44ad-bf8a-9b47625d3967)

Searching for ways to authenticate using the PFX file on WinRM (port 5986), I came across this [IBM blog post](https://www.ibm.com/docs/en/arl/9.7?topic=certification-extracting-certificate-keys-from-pfx-file):

![image](https://github.com/user-attachments/assets/3e979f9f-a7d4-489d-94ab-1afc34d33ff0)

The blog post explained how to extract `.crt` and `.key` files from a PFX file, which can then be used for authentication with WinRM. Following the guide, I realized that it required authentication:

![image](https://github.com/user-attachments/assets/e89b1220-1398-426e-b56f-a9fdef06158e)

To proceed, I used `pfx2john` to convert and crack the password from the PFX file:

```
pfx2john legacyy_dev_auth.pfx > john_pfx
```

Within a few moments, I managed to crack the password:

![image](https://github.com/user-attachments/assets/1f7ee3ec-a955-4d93-a087-0c59e47dbca6)

After obtaining the password, I followed the blog instructions to generate the `.key` and `.crt` files:

```
openssl pkcs12 -in legacyy_dev_auth.pfx -nocerts -out my.key  #utilized abcd as password
openssl pkcs12 -in legacyy_dev_auth.pfx -clcerts -nokeys -out my.crt
```

With both the `.crt` and `.key` files in hand, I used Evil-WinRM to authenticate via WinRM:

```
evil-winrm -i timelapse.htb -S -c my.crt -k my.key
```

After entering the password `abcd` (set during the key file creation), I successfully accessed the system as the user `legacy` and retrieved the user flag. 🙂

![image](https://github.com/user-attachments/assets/0291959f-fc78-4a00-ba7f-38d67231387c)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Root.txt:

After obtaining the user flag, I began searching for ways to escalate privileges. I started with manual enumeration, checking my current privileges, but found that I had no special privileges assigned:

![image](https://github.com/user-attachments/assets/fb35b63c-2714-473c-93e4-952e32fec9a8)

Next, I checked the groups I belonged to but didn't find anything particularly interesting:

![image](https://github.com/user-attachments/assets/db7b1d51-c531-4be1-9358-51f293914fb9)

To further investigate potential privilege escalation paths, I ran `winpeas`. In the PowerShell command history, I found credentials related to a user named `svc_deploy`:

![image](https://github.com/user-attachments/assets/87fb3167-f5bd-4d3e-922b-b59955eedf4c)

Using these credentials, I successfully logged in as the `svc_deploy` user:

![image](https://github.com/user-attachments/assets/abe1188d-6fe3-48e4-90f5-5ab63340c351)

Upon checking the group memberships for `svc_deploy`, I noticed that this user was a member of the `LAPS_readers` group:

![image](https://github.com/user-attachments/assets/c6416d52-9459-409e-a61e-3973c319f2fb)

While researching potential escalation paths, I came across a [HackTricks article](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/laps) that mentioned users with `LAPS_readers` permissions can retrieve LAPS passwords using `crackmapexec`. I followed the guidance and executed the relevant command with the `svc_deploy` credentials, which immediately returned the LAPS password:

![image](https://github.com/user-attachments/assets/4a145b70-760d-4ee8-9efd-de7551b1ed2d)

Using this password, I was finally able to get the access as Administrator & fetched the root flag. (pwn3d!🎉)

![image](https://github.com/user-attachments/assets/82fb38cc-9e24-494e-94c3-0db38b7290db)





