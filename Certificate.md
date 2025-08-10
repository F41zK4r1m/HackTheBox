<img width="669" height="540" alt="image" src="https://github.com/user-attachments/assets/32ebdf6b-a3d2-4f7a-a4de-f26a3528f063" />

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration

## Port Scan

I began the enumeration phase by running a port scan against the target host, which revealed several open ports.

```Rust

PORT      STATE SERVICE               VERSION
53/tcp    open  domain                Simple DNS Plus
80/tcp    open  http                  Apache httpd 2.4.58 (OpenSSL/3.1.3 PHP/8.0.30)
| http-methods: 
|_  Supported Methods: GET HEAD OPTIONS
|_http-title: Did not follow redirect to http://certificate.htb/
88/tcp    open  kerberos-sec          Microsoft Windows Kerberos (server time: 2025-08-03 21:22:33Z)
135/tcp   open  msrpc                 Microsoft Windows RPC
139/tcp   open  netbios-ssn           Microsoft Windows netbios-ssn
389/tcp   open  ldap                  Microsoft Windows Active Directory LDAP (Domain: certificate.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.certificate.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.certificate.htb
| Issuer: commonName=Certificate-LTD-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-11-04T03:14:54
| Not valid after:  2025-11-04T03:14:54
| MD5:   0252:f5f4:2869:d957:e8fa:5c19:dfc5:d8ba
|_SHA-1: 779a:97b1:d8e4:92b5:bafe:bc02:3388:45ff:dff7:6ad2
|_ssl-date: 2025-08-03T21:24:12+00:00; +8h00m01s from scanner time.
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http            Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap              Microsoft Windows Active Directory LDAP (Domain: certificate.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-08-03T21:24:16+00:00; +8h00m02s from scanner time.
| ssl-cert: Subject: commonName=DC01.certificate.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.certificate.htb
| Issuer: commonName=Certificate-LTD-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-11-04T03:14:54
| Not valid after:  2025-11-04T03:14:54
| MD5:   0252:f5f4:2869:d957:e8fa:5c19:dfc5:d8ba
|_SHA-1: 779a:97b1:d8e4:92b5:bafe:bc02:3388:45ff:dff7:6ad2
3268/tcp  open  ldap                  Microsoft Windows Active Directory LDAP (Domain: certificate.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-08-03T21:24:12+00:00; +8h00m01s from scanner time.
| ssl-cert: Subject: commonName=DC01.certificate.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.certificate.htb
| Issuer: commonName=Certificate-LTD-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-11-04T03:14:54
| Not valid after:  2025-11-04T03:14:54
| MD5:   0252:f5f4:2869:d957:e8fa:5c19:dfc5:d8ba
|_SHA-1: 779a:97b1:d8e4:92b5:bafe:bc02:3388:45ff:dff7:6ad2
3269/tcp  open  ssl/globalcatLDAPssl?
|_ssl-date: 2025-08-03T21:24:16+00:00; +8h00m02s from scanner time.
| ssl-cert: Subject: commonName=DC01.certificate.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.certificate.htb
| Issuer: commonName=Certificate-LTD-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-11-04T03:14:54
| Not valid after:  2025-11-04T03:14:54
| MD5:   0252:f5f4:2869:d957:e8fa:5c19:dfc5:d8ba
|_SHA-1: 779a:97b1:d8e4:92b5:bafe:bc02:3388:45ff:dff7:6ad2
5985/tcp  open  http                  Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
9389/tcp  open  mc-nmf                .NET Message Framing
49666/tcp open  msrpc                 Microsoft Windows RPC
49685/tcp open  ncacn_http            Microsoft Windows RPC over HTTP 1.0
49686/tcp open  msrpc                 Microsoft Windows RPC
49687/tcp open  unknown
49706/tcp open  msrpc                 Microsoft Windows RPC
49716/tcp open  msrpc                 Microsoft Windows RPC
49735/tcp open  msrpc                 Microsoft Windows RPC
Service Info: Hosts: certificate.htb, DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 8h00m01s, deviation: 0s, median: 8h00m01s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2025-08-03T21:23:35
|_  start_date: N/A
```
The presence of ports 445 (SMB) and 88 (Kerberos) suggested that the target was part of a Windows domain. Further inspection confirmed the domain name `certificate.htb` and the domain controller’s FQDN `DC01.certificate.htb`.

I then added both entries to my local hosts file to facilitate easier access during the engagement.

## Web Enumeration

Since port 80 was open and a web server was running, I proceeded with web enumeration on the target application.

I began by browsing through the application and noticed that it appeared to be a learning and certification portal:

<img width="1330" height="855" alt="image" src="https://github.com/user-attachments/assets/1e28d6c2-2409-407d-a306-3c3c136a2b15" />

The HTML source code of the portal contained no useful information. To dig deeper, I used `dirsearch` for directory enumeration, which revealed several endpoints such as `login`, `register`, and `upload`:

<img width="683" height="478" alt="image" src="https://github.com/user-attachments/assets/d5b13ce7-b137-4180-872f-fd400b173b4c" />

Accessing the `uploads` directory required authentication. I registered an account as a student and successfully logged in:

<img width="1316" height="829" alt="image" src="https://github.com/user-attachments/assets/730d4294-0da0-47b7-8f0c-a1c5dc8d33f3" />

<img width="1233" height="487" alt="image" src="https://github.com/user-attachments/assets/4476fedb-2f6f-4514-a944-3d7a2ff7f879" />

While exploring the application further, I discovered an option to enroll in courses. I enrolled in one of them and found a feature to submit a quiz:

<img width="919" height="808" alt="image" src="https://github.com/user-attachments/assets/dc1f8778-3a6f-49fa-9f90-ca768a8148ba" />

On inspecting the **Submit Quiz** section, I found a file upload option with restricted allowed extensions:

<img width="1136" height="531" alt="image" src="https://github.com/user-attachments/assets/6fe0acb9-35a8-4fe7-a160-add7b361a23e" />

The permitted file types were: `.pdf`, `.docx`, `.pptx`, `.xlsx`, and `.zip`.

Uploading a malicious `.docx` or `.xlsx` with embedded macros would require manual interaction, which wasn’t feasible in this context. While `.pdf` files couldn’t be used to gain a reverse shell, they could potentially be leveraged for XSS.


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Initial Access

## Zip Slip

While testing the file upload functionality, I noticed that when a clean `.pdf` file was uploaded, it was stored in the following location:  

`/certificate.htb/static/uploads/371dcc2325f3edac50d1371fb8b09481/`

The last directory in the path, consisting of what appears to be a random alphanumeric string, seemed to be generated dynamically for each upload.

<img width="706" height="113" alt="image" src="https://github.com/user-attachments/assets/995effd6-dd9b-4709-9c3b-af26bf704c45" />

I attempted to exploit this behavior by leveraging a `Zip Slip` vulnerability. I prepared a malicious ZIP file containing a PHP reverse shell, using the technique demonstrated in [this video by Nahamsec](https://youtu.be/4sKlbMiGWAw?si=_ZwKk6BEYVGu4-G-).

Unfortunately, this approach was unsuccessful. The application uploaded each file to a newly generated directory, and the PHP reverse shell inside the ZIP was detected and blocked before execution.

## Zip Concatenation

Next, I attempted a [Zip Concatenation](https://se.security.ntt/en/understanding-zip-file-concatenation-a-deep-dive/) attack. This technique involves appending one ZIP archive to another, resulting in a single file containing multiple independent central directories. Since each ZIP archive stores its own central directory at the end, concatenating two or more ZIP files doesn’t technically corrupt them.

To exploit this, I used two files:  
- A clean PDF file  
- A folder containing a PHP reverse shell  

I followed these steps to create a concatenated ZIP:

```
zip clean clean_pdf.pdf # Zipping the clean PDF file
zip -r rev php_shell # Zipping the php_shell folder (contains PHP reverse shell)
cat clean.zip rev.zip > final.zip # Concatenating both zips into a single file
```

Once `final.zip` was ready, I uploaded it to the portal. The application successfully accepted the file, but the provided view link only displayed the PDF:

<img width="940" height="161" alt="image" src="https://github.com/user-attachments/assets/3c34f3db-01be-4073-a58f-7cb7a3285d7f" />

I then manually modified the URL to point to my PHP reverse shell:

<img width="808" height="95" alt="image" src="https://github.com/user-attachments/assets/4820a0f7-2771-432d-93c1-e78fc8c86fa3" />

Once the PHP executed in the background, I received a reverse shell connection from the target host, with the session running under the user `certificate\xamppuser`:

<img width="836" height="204" alt="image" src="https://github.com/user-attachments/assets/22e376f5-5b21-435d-bc4e-df529d6e2f1a" />

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Lateral Movement

## From `xamppuser` to `sara.b`

To begin lateral movement and explore privilege escalation opportunities, I started with manual enumeration. Checking the privileges of the `xamppuser` account revealed that it had no special privileges assigned:

<img width="1011" height="725" alt="image" src="https://github.com/user-attachments/assets/c6c287ab-0c90-4024-9dff-b6266210a479" />

Next, I navigated to the `certificate.htb` directory. From the earlier web enumeration phase, I had noted the presence of a `db.php` file within the application.

<img width="507" height="451" alt="image" src="https://github.com/user-attachments/assets/a1e44208-94db-4d5b-a548-6c9522d292bc" />

Upon examining the `db.php` file, I discovered MySQL database credentials:

<img width="660" height="286" alt="image" src="https://github.com/user-attachments/assets/471db2f0-b9cc-4096-96da-6b6eb321c355" />

### SQL Dump

After retrieving the clear-text credentials, I attempted to dump the database. First, I located the MySQL binaries inside:

```
C:\xampp\mysql\bin
```

Initially, using `mysql.exe` or `mysqldump.exe` directly did not work as expected. After some troubleshooting and trial-and-error, I successfully dumped the database using the following command:

```
C:\xampp\mysql\bin\mysqldump.exe -u certificate_webapp_user -p"*********" Certificate_WEBAPP_DB > dump.sql
```

Once the dump was created, I moved it into the folder containing my PHP reverse shell. Finally, I downloaded it via the browser:

<img width="1014" height="260" alt="image" src="https://github.com/user-attachments/assets/9f58893a-fbdf-4b9d-947a-c956ac2db2d6" />


### Hash Cracking

After downloading the file to my Kali host, I opened it in a text editor and identified several usernames along with their password hashes:

<img width="1372" height="184" alt="image" src="https://github.com/user-attachments/assets/797b4ce3-c473-4bff-a82d-df715999c3c9" />

I extracted all the hashes and attempted to crack them using **John the Ripper**. From these, I successfully cracked the hash for the account **sara.b@certificate.htb**:

<img width="294" height="59" alt="image" src="https://github.com/user-attachments/assets/1585ebf7-e7d9-4dc0-bc1c-6058db3b046d" />

Using the recovered password, I gained **WinRM** access to the target system:

<img width="1061" height="72" alt="image" src="https://github.com/user-attachments/assets/bc11a0f6-dfe6-4868-bfaa-cdf56f0bd26d" />

However, upon checking Sara’s Desktop, I found no user flag — indicating that I needed to escalate further to retrieve it.

## Sara.b -> Lion.SK

Moving further, when I checked the **Documents** directory for Sara, I observed a folder named `WS-01`.  
Inside this folder, I found a **PCAP** file and a text file.  

The text file contained the following content:

![Text File Content](https://github.com/user-attachments/assets/35700bef-f7c7-4cfa-83f4-3a1e597ac696)

I proceeded to download the PCAP file for further analysis.


### PCAP Analysis

I analyzed the PCAP file using **Wireshark**, where I noticed NTLM and Kerberos communication between `DC01` and the `WS-01` host:

![NTLM and Kerberos Traffic](https://github.com/user-attachments/assets/636e5693-284a-465d-983a-a84599fe40bb)

---

#### NTLMv2 Hash Extraction

Since NTLMv2 hashes are generally easier to crack, I attempted to extract them.  
For this, I used [NTLMRawUnhide](https://github.com/mlgualtieri/NTLMRawUnhide), which parses NTLM hashes directly from a PCAP file.

Using the tool, I successfully extracted the NTLMv2 hashes for the **Administrator** account from `WS-01`:

![Extracted NTLMv2 Hashes](https://github.com/user-attachments/assets/98776183-00e6-4dc6-bafb-55972482b0eb)

Unfortunately, I was unable to crack these hashes:

![Failed NTLM Crack](https://github.com/user-attachments/assets/b42f857e-85fd-49a9-a579-ee973b760984)

---

#### Kerberos Ticket Extraction

Next, I shifted focus to Kerberos tickets present in the capture.  
After some research, I found [Krb5RoastParser](https://github.com/jalvarezz13/Krb5RoastParser.git), which can extract Kerberos hashes from PCAP files.

Using the tool, I was able to extract Kerberos hashes for `Lion.SK`:

![Kerberos Hashes 1](https://github.com/user-attachments/assets/b3c1fbf9-aa2e-4811-83ac-712e9e29f4af)  
![Kerberos Hashes 2](https://github.com/user-attachments/assets/0f4be94f-a598-48a0-a0b3-069ef4ef5eaf)  
![Kerberos Hashes 3](https://github.com/user-attachments/assets/d54bd0ba-efd8-4484-a18c-d94aa7458da4)

Initially, cracking attempts failed.  
However, I noticed that the `AS-REQ` hash contained an **incomplete domain name**, so I manually appended `.htb` to the hash and retried cracking.

This time, the hash cracked successfully, revealing the clear-text password:

![Cracked Password](https://github.com/user-attachments/assets/e7e6ee2c-5559-4bc9-93d8-72f29d463473)

---

#### Access as `Lion.SK`

With valid credentials in hand, I logged in via the **WinRM protocol** and retrieved the **user flag**:

![User Flag](https://github.com/user-attachments/assets/36657c17-1cba-4135-b2a9-11daa3ed9870)


## Lion.sk -> Ryan.k

### BloodHound:

Moving on, I ran `SharpHound` collector to analyze the Active Directory structure in a graph and identify possible escalation paths to Domain Admin.

I uploaded the SharpHound binary and executed it to collect all the necessary information:

![SharpHound Execution](https://github.com/user-attachments/assets/16391230-f19a-47ce-bcd9-ae32e35468d6)

![SharpHound Data Collection](https://github.com/user-attachments/assets/04207c4a-adc0-4f92-9009-33a55ca15d22)

I analyzed the results in BloodHound, but there were no direct paths available to escalate privileges to Domain Admin.

![BloodHound Analysis](https://github.com/user-attachments/assets/e546fb6e-3656-4980-98ab-a211112433d1)

### Active Directory Certificate Services (AD CS) Enumeration

Since no direct escalation path was identified in BloodHound, I shifted focus to **Active Directory Certificate Services** (AD CS) enumeration, using the hostname hint as a lead. I utilized **Certipy** to look for vulnerable certificate templates.

> **Note:** The target machine was extremely slow, with LDAP communication failing approximately 9 out of 10 times. Due to this instability, I had to reset the box 2–3 times before successfully retrieving certificate data.

I ran the following command to enumerate AD CS and search for vulnerable templates:

```bash
certipy -debug find -vulnerable -u 'lion.sk@certificate.htb' -p '******' -dc-ip 10.10.11.71 -dc-host dc01.certificate.htb -target 10.10.11.71 -stdout -timeout 45
```

From the Certipy output, I found one vulnerable certificate template: **Delegated-CRA**, which had an ESC3 privilege escalation path.

<img width="912" height="761" alt="image" src="https://github.com/user-attachments/assets/d0cda215-b7e1-45b9-82bc-03c941b4222c" />


#### ADCS Exploitation (ESC3)

After identifying the vulnerable `Delegated-CRA` template, I verified my privileges as `Lion.Sk` and confirmed from BloodHound that the user was part of the **Domain CRA Managers** group.  
Following the [Certipy wiki](https://github.com/ly4k/Certipy/wiki/06-%E2%80%90-Privilege-Escalation), I proceeded with the ESC3 escalation.

Requesting a certificate using the vulnerable template:  

```
certipy -debug req -u 'lion.sk@certificate.htb' -p '*********' -dc-ip '10.10.11.71' -target 'DC01.certificate.htb' -ca 'Certificate-LTD-CA' -template 'Delegated-CRA'
```

<img width="866" height="217" alt="image" src="https://github.com/user-attachments/assets/0fd23210-7145-49da-a9c8-785427331c36" />

With the retrieved `lion.sk.pfx` Enrollment Agent certificate, I attempted to request a certificate on behalf of `Administrator`:

```
certipy req -u 'lion.sk@certificate.htb' -p '*****' -dc-ip '10.10.11.71' -target 'dc01.certificate.htb' -ca 'Certificate-LTD-CA' -template 'User' -pfx 'lion.sk.pfx' -on-behalf-of 'certificate\Administrator'
```

<img width="1453" height="137" alt="image" src="https://github.com/user-attachments/assets/042b63c2-30e1-4dbf-8d3a-8306c9b63539" />

This failed since the `User` template was inactive. I enumerated available certificates again and found `SignedUser` as a valid option:  

<img width="742" height="754" alt="image" src="https://github.com/user-attachments/assets/43d04f08-861c-4973-a9e5-8faa70f67fe6" />

I retried the request using the new template:  

<img width="1637" height="140" alt="image" src="https://github.com/user-attachments/assets/d7232396-a706-4d80-a466-eac8b1d4e9c8" />

This also failed with the error **"email name is unavailable"**. I tried with the `dc01$` machine account, but it didn’t work either.  
I then enumerated AD users (17 total) and checked privileged groups for non-Administrator members. I discovered that the **Domain Storage Manager** group contained only `Ryan.k`:  

<img width="900" height="773" alt="image" src="https://github.com/user-attachments/assets/0ac68c26-031e-4b79-999a-e314c748dd5d" />  
<img width="982" height="621" alt="image" src="https://github.com/user-attachments/assets/108b8173-406a-4d26-b589-54bec09c4113" />

Requesting a certificate on behalf of `Ryan.k`:  

<img width="1731" height="182" alt="image" src="https://github.com/user-attachments/assets/799be972-66bc-419d-a095-01307d3215d1" />

Finally, I authenticated to the DC and retrieved the NTLM hash for `Ryan.k`:

```
certipy -debug auth -pfx ryan.k.pfx -dc-ip 10.10.11.71
```

<img width="1132" height="423" alt="image" src="https://github.com/user-attachments/assets/c9f4929e-4885-4c85-8287-cd3df798a016" />

# Privilege Escalation

## Ryan.k -> Administrator

After obtaining the hashes, I performed a pass-the-hash attack using `evil-winrm` and successfully gained a shell as `Ryan.k`. To escalate my privileges, I reviewed the local privileges assigned to the user and identified a unique privilege: `SeManageVolumePrivilege`. This privilege appeared to be related to domain storage management.

<img width="1280" height="632" alt="image" src="https://github.com/user-attachments/assets/8739c0ed-bd74-47a9-9984-343896fd0674" />

While researching escalation methods, I came across [this blog](https://motasemhamdan.medium.com/active-directory-pentesting-offensive-security-proving-grounds-access-writeup-ddf4f3c6fcb9), which describes the same escalation technique in a different lab environment.

I downloaded [SeManageVolumeExploit.exe](https://github.com/CsEnox/SeManageVolumeExploit), a tool that, when executed, grants full permissions on the `C:\` drive to all users on the machine.

I executed the binary and confirmed that I now had full access to the `C:\` drive:

<img width="740" height="218" alt="image" src="https://github.com/user-attachments/assets/fba38a54-972e-45f1-8cac-3913c40325b6" />

After gaining access, I attempted the `PrinConfig.dll` and `tzres.dll` exploits by referencing [this blog](https://hackfa.st/Offensive-Security/Windows-Environment/Privilege-Escalation/Token-Impersonation/SeManageVolumePrivilege/). However, both attempts failed because the required DLL hijacking conditions were restricted.

### Golden Certificate

After failing to escalate privileges via the DLL hijack technique, I pivoted to inspecting the local host for certificates.  
Since this machine also served as a **Certificate Authority (CA)**, obtaining its private key would allow me to perform a [Golden Certificate](https://www.hackingarticles.in/domain-persistence-golden-certificate-attack/) attack.

This technique abuses **Active Directory Certificate Services (ADCS)**, which enables certificate-based authentication by default.  

By extracting the CA’s private key, it becomes possible to forge a valid certificate for any account in the domain—including **Domain Admins**—without needing their password or NTLM hash.

I began by enumerating the locally available certificates:

```
certutil -Store My
```

The output revealed three certificates whose private keys I could export due to my full privileges over C:\.
One of these was the CA certificate, which I could leverage for the Golden Certificate attack.

<img width="889" height="832" alt="image" src="https://github.com/user-attachments/assets/490314c3-91d1-4ccd-9913-164a22bfe211" />

So, using the certutil command, I then exported the CA certificate to a .pfx file:

```
certutil -exportPFX my "Certificate-LTD-CA" ca.pfx
```

<img width="852" height="511" alt="image" src="https://github.com/user-attachments/assets/e58548b7-eb59-4ef2-bfb1-7b42826a1462" />

Using this .pfx file, I forged a Golden Certificate for the Administrator account, following [certipy's wiki](https://github.com/ly4k/Certipy/wiki/07-%E2%80%90-Post%E2%80%90Exploitation#forging-golden-certificates-leveraging-a-compromised-ca-key):

```
certipy forge -ca-pfx 'ca.pfx' -upn 'administrator@certificate.htb' -sid 'S-1-5-21-515537669-4223687196-3249690583-500' -crl 'ldap:///'
```

<img width="1126" height="99" alt="image" src="https://github.com/user-attachments/assets/79c2777b-3dd0-4b67-a67f-119647c99d26" />

Next, I authenticated to the Domain Controller using the forged certificate and retrieved the Administrator’s NTLM hash:

```
certipy auth -pfx administrator_forged.pfx -dc-ip 10.10.11.71
```

<img width="1050" height="241" alt="image" src="https://github.com/user-attachments/assets/c3b1341c-f0f2-4241-a84e-a3741804d1cc" />

And finally with this hash, I was able to access the Administrator’s desktop and retrieve the root flag:

<img width="1209" height="91" alt="image" src="https://github.com/user-attachments/assets/a735f8df-6eeb-4cd1-8320-411418dcea4b" />


