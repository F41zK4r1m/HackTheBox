![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4c13ba4f-ca9b-4dd8-aecf-a11d9c732328)

https://app.hackthebox.com/machines/Academy

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

To initiate the assessment of the target machine, I utilized a quick rustscan, which revealed the presence of only three open ports: 22, 80, and 33060:

```Rust
PORT      STATE SERVICE REASON         VERSION
22/tcp    open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 c0:90:a3:d8:35:25:6f:fa:33:06:cf:80:13:a0:a5:53 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC/0BA3dU0ygKCvP7G3GklCeOqxb17vxMCsugN05RA9Fhj7AzkPiMLrrKRY656gBuscH23utAWAhRXzV1SyU37bbFzEbfaqYAlh1ggHEuluLgbf9QsYZe76zCx2SRPOzoI9Q40klVvuu9E92pNLe80dvUZj644EwhJTGw4KGxeOqeuo/nXnYfiNAbWvOe9Qp+djDbEvP5lHwIDMTAtgggoSC1chubC3jFC4hihuYjtitjUr4+5fROomhJAo/GEvdBj2CYNHIFEvmuvb32cgul5ENQS4fJXpcI7fbP9/+b/cfA9oRxG2k+k1M8mUld2h5mHEVBE5Z9WKS3cRYu97oVKnRRCoDY/55mZw6lngIdH4drpYwzCrZcCWgviXRfCeOwmZ8sucap6qN/nFYnPoF7fd+LGaQOhz9MkAZCTMmLqSiZGSistAIPzHtABH0VQDbo2TqJ+kGWr9/EamCcYBbVVPaFj/XQqujoEjLYW+igihwrPEQ7zxlleQHwg91oSVy38=
|   256 2a:d5:4b:d0:46:f0:ed:c9:3c:8d:f6:5d:ab:ae:77:96 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAIMsz8qKL1UCyrPmpM5iTmoy3cOsk+4L7oFdcPjBXwAcUVvnti7nXHlNqMfgsapbGSIl7AWTOeXLZmw2J6JWvE=
|   256 e1:64:14:c3:cc:51:b2:3b:a6:28:a7:b1:ae:5f:45:35 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHBP1E2rWeTShvyJKxC5Brv1Do3OwvWIzlZHWVw/bD0R
80/tcp    open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Did not follow redirect to http://academy.htb/
33060/tcp open  mysqlx? syn-ack ttl 63
| fingerprint-strings: 
|   DNSStatusRequestTCP, LDAPSearchReq, NotesRPC, SSLSessionReq, TLSSessionReq, X11Probe, afp: 
|     Invalid message"
|_    HY000
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port33060-TCP:V=7.91%I=7%D=8/4%Time=64CD558B%P=x86_64-unknown-linux-gnu
SF:%r(NULL,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(GenericLines,9,"\x05\0\0\0\
SF:x0b\x08\x05\x1a\0")%r(GetRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(HT
SF:TPOptions,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(RTSPRequest,9,"\x05\0\0\0
SF:\x0b\x08\x05\x1a\0")%r(RPCCheck,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNS
SF:VersionBindReqTCP,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNSStatusRequestT
SF:CP,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\
SF:x0fInvalid\x20message\"\x05HY000")%r(Help,9,"\x05\0\0\0\x0b\x08\x05\x1a
SF:\0")%r(SSLSessionReq,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08
SF:\x01\x10\x88'\x1a\x0fInvalid\x20message\"\x05HY000")%r(TerminalServerCo
SF:okie,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(TLSSessionReq,2B,"\x05\0\0\0\x
SF:0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20messa
SF:ge\"\x05HY000")%r(Kerberos,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(SMBProgN
SF:eg,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(X11Probe,2B,"\x05\0\0\0\x0b\x08\
SF:x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\x0
SF:5HY000")%r(FourOhFourRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LPDStr
SF:ing,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LDAPSearchReq,2B,"\x05\0\0\0\x0
SF:b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20messag
SF:e\"\x05HY000")%r(LDAPBindReq,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(SIPOpt
SF:ions,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LANDesk-RC,9,"\x05\0\0\0\x0b\x
SF:08\x05\x1a\0")%r(TerminalServer,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(NCP
SF:,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(NotesRPC,2B,"\x05\0\0\0\x0b\x08\x0
SF:5\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\x05H
SF:Y000")%r(JavaRMI,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(WMSRequest,9,"\x05
SF:\0\0\0\x0b\x08\x05\x1a\0")%r(oracle-tns,9,"\x05\0\0\0\x0b\x08\x05\x1a\0
SF:")%r(ms-sql-s,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(afp,2B,"\x05\0\0\0\x0
SF:b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20messag
SF:e\"\x05HY000")%r(giop,9,"\x05\0\0\0\x0b\x08\x05\x1a\0");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

On port 80, I noticed a domain named "download.htb," which I promptly added to my hosts configuration file.

Following the addition of the domain to the hosts configuration file, I proceeded to perform fuzzing on sub-directories and virtual hosts, but unfortunately, I did not observe any significant findings.

## Initial access:

During my initial exploration of the website, I noticed that it closely resembles the original HTB Academy website:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b825046d-7cd6-49ce-a103-17e53193ec28)

Upon further investigation, I found a "register" and "login" option. Intrigued by the opportunities this presented, I decided to use Burp to intercept the registration request.

In the intercepted request, I discovered a parameter labeled "roleid," which was set to "0":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/41c2dd71-8e1e-4c8b-abe1-f01233e96f7f)

I then proceeded to register myself once again, this time altering the "roleid" to "1." This simple modification allowed me to register as an admin user:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2098a51f-3367-49af-b12b-e3a403dd1ca5)

Using the newly acquired admin credentials, I successfully logged into the "/admin.php" site:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2bee9f22-9312-4de1-95dc-c3f63e3364c0)

Within the admin panel, I noticed the existence of another domain, "dev-staging-01.academy.htb." Intrigued by this discovery, I added the domain to my hosts configuration file and proceeded to browse through it:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/bf15e951-224d-4d64-9d36-45b4b7edb962)

### Shell access:

While exploring the "dev-staging-01.academy.htb" staging environment, I made a significant discovery – an application running on Laravel, which exposed its "app_key." This piqued my interest, and I began searching for any related Laravel exploits. My search led me to a promising exploit on [Github](https://github.com/kozmic/laravel-poc-CVE-2018-15133) that explained a Remote Code Execution (RCE) vulnerability in the Laravel application:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d18c32d1-9fff-4ff0-95df-6fe6476cc161)

I also came across another [Github](https://github.com/aljavier/exploit_laravel_cve-2018-15133/tree/main) repository that provided a Python-based Proof of Concept (PoC) for this exploit.

I decided to utilize the Python-based exploit, running the following command:

```python3
python3 pwn_laravel.py http://dev-staging-01.academy.htb/ dBLUaMuZz7Iq06XtL/Xnz/90Ejq+DEEynggqubHWFj0= --interactive
```
The Python exploit proved to be effective, granting me a shell as the "www-data" user:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b47b9173-d235-4caf-ae22-e1a0d6c49a65)

## User flag:

During my enumeration of the "/home" directory, I discovered multiple users present:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a87c8cb0-6cc4-4062-b9cd-67d6bf1ccba1)

While investigating further, I noticed the user flag in the "cry0l1t3" directory. Unfortunately, I did not have access to this directory at that moment:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0c14cee1-3ce8-4555-be0b-77707f2400e4)

To acquire the user flag, I decided to perform manual enumeration in search of potential credentials. My efforts led me to the "/var/www/html/academy" directory, where I discovered an environment file containing a password for the "dev" user:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4030066e-0cd3-4156-a95f-88b2d2b61054)

Considering the possibility of password reuse, I attempted to use these credentials to SSH into the "cry0l1t3" account – and successfully gained access:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/207c2bfe-571f-4f9e-8815-92c38896631a)

After successfully logging in, I was able to obtain the user flag. (pwn3d! 🙂)

## Privilege escalation:

While inspecting the groups associated with the "cry0l1t3" user, I noticed that he belonged to the "adm" group. Upon researching the roles and responsibilities of this group, I found a [Debian wiki page](https://wiki.debian.org/SystemGroups) that shed light on its purpose:

```
Group adm is used for system monitoring tasks. Members of this group can read many log files in /var/log, and can use xconsole.
Historically, /var/log was /usr/adm (and later /var/adm), thus the name of the group. 
```

While reviewing the audit logs located in the "/var/log/audit" directory, I was manually searching for any sensitive text or information. However, during my research, I came across the [0xdf writeup](https://0xdf.gitlab.io/2021/02/27/htb-academy.html%22) which introduced me to the "**aureport**" tool. This tool allows for the generation of summary reports from the audit system logs.

Utilizing the "aureport" tool, I focused on analyzing the audit logs for "tty" keystrokes and managed to uncover the password for the user "**mrb3n**":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/8f3e0bec-71f9-4091-953d-e0c6388f9a39)

With these credentials in hand, I successfully gained access to the "mrb3n" account:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/59d57e8b-627d-4607-ad07-2e6cc623f825)

### Root flag:

Once I gained access as the user "mrb3n," I proceeded to check the sudo privileges assigned to this account and observed that:

```bash
sudo -l
```

```
Matching Defaults entries for mrb3n on academy:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User mrb3n may run the following commands on academy:
    (ALL) /usr/bin/composer
```

Based on my findings, I referred to the [GtfoBins](https://gtfobins.github.io/gtfobins/composer/#sudo) page to explore potential privilege escalation using the "composer" binary. From the information provided, I learned that I could utilize the following command to gain a root shell:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ce2d8b59-5dc5-40dd-836d-dbe856c414c2)

Following the steps mentioned in the reference, I executed the command and successfully obtained a root shell. As a result, I gained access as the root user and obtained the root flag: (pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/bb868645-bc21-412f-acc2-704ba1887869)

