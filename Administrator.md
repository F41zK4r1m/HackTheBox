![image](https://github.com/user-attachments/assets/e58bee8d-30a5-41fb-86f0-bda461c3ea2d)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration:

I began the enumeration of the host and noticed that a credential was provided with the box, simulating a real-world pentest scenario starting with an assumed breach.

![image](https://github.com/user-attachments/assets/2fad31d4-ad2b-4bf9-b165-3070cc16f629)

## Port scan:

I initiated the enumeration using `nmap`, which revealed multiple open ports on the host. Since ports `88` and `53` were open and running, it was highly likely that the host was a Domain Controller.

```Rust
└─$ nmap -sVC 10.10.11.42                                     
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-16 13:15 EDT
Stats: 0:00:51 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 24.94% done; ETC: 13:18 (0:02:33 remaining)
Stats: 0:01:11 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 30.36% done; ETC: 13:18 (0:02:41 remaining)
Stats: 0:03:03 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 53.41% done; ETC: 13:20 (0:02:40 remaining)
Stats: 0:05:33 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 74.00% done; ETC: 13:22 (0:01:57 remaining)
Nmap scan report for 10.10.11.42
Host is up (0.22s latency).
Not shown: 988 closed tcp ports (conn-refused)
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-03-17 00:24:09Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: administrator.htb0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: administrator.htb0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-03-17T00:24:25
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 7h00m00s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 578.72 seconds
```

From the port scan results, I discovered a domain running on the host: `administrator.htb`. I added this domain to my hosts configuration file.

## FTP Enumeration:

After the port scan, I proceeded to enumerate the FTP service. I first attempted to authenticate using an anonymous login, but it didn’t work.

![image](https://github.com/user-attachments/assets/d4ab0adb-e088-47de-8111-40864ad1aea7)

Next, I tried logging in with the credentials provided in the box info: `Olivia : ichliebedich`, but this also failed.

![image](https://github.com/user-attachments/assets/dbe38fe8-0d82-4592-8c28-f537db41d104)

## AS-REP Roasting:

Next, I performed an AS-REP roasting attack, which checks for any accounts with the `PRE-AUTH not required` flag set. However, no accounts with this flag were found.

![image](https://github.com/user-attachments/assets/b0da1433-37da-45db-a78a-bd5acd42fcf3)

## Kerberoasting:

Next, I attempted a Kerberoasting attack to identify accounts with a Service Principal Name (SPN) set. This would allow me to request service tickets and crack them offline to obtain clear-text passwords. However, no objects with an SPN set were found.

![image](https://github.com/user-attachments/assets/64e47ffb-38af-44ca-942c-c70a2f1eb546)

## User Enumeration:

Next, I proceeded to enumerate the users present in the domain. I used the SMB protocol to list the users and found a total of 10 accounts.

![image](https://github.com/user-attachments/assets/2fb212a9-548c-4934-99ea-44d6117731fa)

However, this result didn’t confirm whether all the users were active. To specifically identify the active users, I used the LDAP protocol, which revealed a total of 6 active users.

![image](https://github.com/user-attachments/assets/157d4d0a-b980-4fd4-9ee6-73a46e5fc526)

## Password Policy:

Next, I enumerated the domain password policy and observed that no lockout threshold was set. This meant I could perform brute-force attacks on any user account without the risk of getting locked out.

![image](https://github.com/user-attachments/assets/20c5cd44-61c2-4454-a707-a8274afaa9fc)

## Password Spray:

After reviewing the password policy, I performed a password spray attack by using the same password across all valid accounts. However, none of the accounts were using this password.

![image](https://github.com/user-attachments/assets/8c550102-ec45-4ce4-b09f-3a2b03e0f8ec)

Next, I attempted the `USER:USER` credential combination, but this also failed.

![image](https://github.com/user-attachments/assets/c538974b-32eb-4256-8875-729def02cd4d)

## SMB Share enumeration:

Next, I proceeded to enumerate the file shares, aiming to find valuable information that could help me gain access to the host or compromise another account for lateral movement. I discovered three folders with read access:

![image](https://github.com/user-attachments/assets/0609c7ad-15c1-4ac4-9b3e-b38325139b13)

However, all of these folders were default shares and did not contain any information that could be leveraged for lateral movement.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Initial access:

While checking for access over the `winrm` protocol, I discovered that the user `Olivia` had access to the host via winrm.

![image](https://github.com/user-attachments/assets/f7b1a380-6d39-4b5d-af62-cb0c8fcb401d)

I quickly logged in using `evil-winrm` and gained initial access.

![image](https://github.com/user-attachments/assets/1864a1a8-996b-407c-a81e-20ea3989c7bc)

Although I successfully accessed the host, the `Olivia` user did not have sufficient permissions to perform privilege escalation.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Lateral movement:

## Bloodhound

To identify privilege escalation opportunities and facilitate lateral movement within the domain, I decided to run **BloodHound** to collect and visualize domain data. I used the `bloodhound-python` utility to gather information from the domain.

```
bloodhound-python -u olivia -p ichliebedich -d administrator.htb -ns 10.10.11.42 -c All --zip
```
![image](https://github.com/user-attachments/assets/e7c029eb-6596-45f9-baaa-202d112e0e5a)

Once the data was collected, I uploaded it into BloodHound.

![image](https://github.com/user-attachments/assets/d85e7bec-c3a1-4c00-baba-0fb928b88af8)

Within BloodHound, I observed that the `Olivia` user had **GenericAll** control over the `Michael` user:

![image](https://github.com/user-attachments/assets/c42289fb-7b68-4fd8-9ad4-365bd3a713eb)

This meant that `Olivia` had full control over `Michael`, allowing me to change or reset his domain password.

![image](https://github.com/user-attachments/assets/e2524159-3e86-4ca3-8d80-a1a727fd94fa)

## Password reset & owning Michael:

I leveraged the `GenericAll` access and used evil-winrm to reset Michael's credentials using the `net user` command.

![image](https://github.com/user-attachments/assets/ad9d1425-fd38-496e-a977-c6c26f3715fe)

I successfully changed Michael's credentials.

![image](https://github.com/user-attachments/assets/8bbc2f69-d4ee-425b-8cb7-74642d2d4257)

## Michael --> Benjamin:

After logging into Michael's account, I checked his privileges but didn’t find anything that would help me escalate my privileges.

![image](https://github.com/user-attachments/assets/153b964f-6c3c-4cae-8dff-9f09da4fd23f)

I revisited BloodHound and discovered that Michael had the privilege to force a password change for Benjamin's account:

![image](https://github.com/user-attachments/assets/eef84c8c-23db-43f4-b650-3bd9a961ed56)

I attempted to reset Benjamin's password using the `net user` command again, but this time it didn’t work. Instead, I used `rpcclient` to perform the password reset:

```
rpcclient -U michael administrator.htb                 
Password for [WORKGROUP\michael]:

rpcclient $> setuserinfo2 benjamin 23 'Passw0rd@123#'
```

This method worked successfully, and I gained access to the `Benjamin` account:

![image](https://github.com/user-attachments/assets/0c7019ec-b4d0-4910-827c-65c17c2862c5)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Privilege Escalation:

After gaining access as `Benjamin`, I checked BloodHound again to identify any additional users with misconfigurations but didn’t find anything useful.

Next, I examined the file shares associated with Benjamin, but there were no new or valuable shares available.

![image](https://github.com/user-attachments/assets/7b825b77-db3b-4b07-bb68-75ccb76addbd)

I then enumerated FTP access and observed that Benjamin had access to the FTP service.

![image](https://github.com/user-attachments/assets/4826faf9-6d22-47cb-8ff4-48083c255e4c)

After authenticating to the FTP share, I discovered a file named `Backup.psafe3`, which I downloaded locally.

![image](https://github.com/user-attachments/assets/3a3147cb-efa6-481e-b496-a54850b67d0d)

## Password cracking:

Upon examining the format of the downloaded file, I identified it as a Password Safe file. After some research, I came across this [GitHub](https://github.com/pwsafe/pwsafe) repository for Password Safe.

I downloaded the released binary from GitHub and attempted to open the file. However, it prompted me for a password.

![image](https://github.com/user-attachments/assets/14b9d895-5844-462b-8d9c-a728d337d521)

Continuing my research, I found that **John the Ripper** has a module capable of handling this file type for password cracking.

I converted the file into **John the Ripper** format and then proceeded to crack the password:

```
pwsafe2john Backup.psafe3 > john_pwsafe

john --w=/usr/share/wordlists/rockyou.txt john_pwsafe
```

![image](https://github.com/user-attachments/assets/0fbd06ad-7efe-44bb-8db0-d2320c9a71e9)

Once the password was successfully cracked, I gained access to the Password Safe.

![image](https://github.com/user-attachments/assets/6b792958-757f-447b-aa72-195bc7c2b4d0)

## Emily -> Ethan:

The **Password Safe** contained credentials for three users, but only `Emily's` account was active. I tested Emily's credentials, which successfully worked.

![image](https://github.com/user-attachments/assets/1e674698-2fb9-417e-ad2b-de411630f7da)

Upon analyzing BloodHound, I observed that Emily had `GenericWrite` access over `Ethan`, who was a member of the **Administrators** group in the domain.

![image](https://github.com/user-attachments/assets/36debd0d-c32b-4755-aa57-0113d39b33c2)

Leveraging this misconfiguration, I used a tool called [TargetedKerberoast](https://github.com/ShutdownRepo/targetedKerberoast), which assigns temporary credentials, adds SPNs, and fetches the Kerberoast ticket hash.

However, while performing the Kerberoasting attack, I encountered the following error:
`Kerberos SessionError: KRB_AP_ERR_SKEW (Clock skew too great)`

To resolve this, I synchronized the time with the domain controller using the following command:

```
sudo ntpdate administrator.htb
```

After running the command, I performed the **TargetedKerberoast** attack again and successfully obtained the Kerberoast ticket:

![image](https://github.com/user-attachments/assets/6edb32f4-1999-47ba-a7ee-07a265db8105)

I cracked this ticket quickly with John-the-ripper.

```
john --w=/usr/share/wordlists/rockyou.txt --format=krb5tgs kerb.out
```

![image](https://github.com/user-attachments/assets/855f410f-760c-414f-9c7c-6806cf03972d)

## DcSync as Ethan:

Once I successfully cracked the credentials, I verified them, and they worked without any issues.

![image](https://github.com/user-attachments/assets/9198cece-df78-4a18-a1a2-8611c33187dd)

To perform DcSync, I used `secretsdump`, which allowed me to dump all the hashes from the domain controller.

![image](https://github.com/user-attachments/assets/7cd757c1-b8a0-45a0-821b-720657c4dfcc)

After successfully dumping all the credentials, I obtained the **Administrator's hash**, which I then used to log in via **Evil-WinRM**.

Finally, I retrieved both flags from the box. (pwn3d! 🎉)

