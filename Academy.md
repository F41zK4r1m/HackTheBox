![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4c13ba4f-ca9b-4dd8-aecf-a11d9c732328)

https://app.hackthebox.com/machines/Academy

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I started with the quick rustscan & observed only 3 open ports 22, 80 & 33060:

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

On port 80 I observed a domain "download.htb", which I added to my hosts config file.

After adding the domain to the hosts config file I performed fuzzing sub-directory & vhost but didn't observed anything.

## Initial access:

I browsed through the website & observed that it's a replica of the original HTB academy website:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b825046d-7cd6-49ce-a103-17e53193ec28)

I observed that there is a register & login option, so I just fired up my Burp & registered myself on the academy. While intercepting the request I observed that there is a "roleid" parameter in the request which is set to "0".

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/41c2dd71-8e1e-4c8b-abe1-f01233e96f7f)

So, next thing I did was registered my self again but changed the "roleid=1". This change in role id from 0 to 1 registered me as an admin user.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2098a51f-3367-49af-b12b-e3a403dd1ca5)

I used these new credentials to logon as admin on "/admin.php" site & logged in successfully:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2bee9f22-9312-4de1-95dc-c3f63e3364c0)

After logging in to the admin panel I observed that there is another dev staging domain available "dev-staging-01.academy.htb". I added this domain to my hosts config file & browsed thorugh it:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/bf15e951-224d-4d64-9d36-45b4b7edb962)

### Shell access:

When browsing through this staging environment I observed that there is an app running "Laravel", which also exposed "app_key". Searching for the exploits related to "laravel" I found this on [Github](https://github.com/kozmic/laravel-poc-CVE-2018-15133).

This explains about the RCE in the Laravel application:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d18c32d1-9fff-4ff0-95df-6fe6476cc161)

In another [Github](https://github.com/aljavier/exploit_laravel_cve-2018-15133/tree/main) repo I got the python based poc for this exploit.

```python3
python3 pwn_laravel.py http://dev-staging-01.academy.htb/ dBLUaMuZz7Iq06XtL/Xnz/90Ejq+DEEynggqubHWFj0= --interactive
```

Using the python based exploit, I finally got the shell as user "www-data":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b47b9173-d235-4caf-ae22-e1a0d6c49a65)

## User flag:

While enumerating the "/home" directory I found multiple users in it:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a87c8cb0-6cc4-4062-b9cd-67d6bf1ccba1)

But observed user flag in the "cry0l1t3" directory, for which I haven't the access:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0c14cee1-3ce8-4555-be0b-77707f2400e4)

So, to get the user flag, I started manual enumeration to search for some credentials. While looking into "/var/www/html/academy" I found a environment file, which contains a password for the dev user:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4030066e-0cd3-4156-a95f-88b2d2b61054)

Since, the password belongs to a developer & but there is a possibility of the password re-use & since "cry0l1t3" contains user flag, I tried the same credentials in SSH for "cry0l1t3" & logged in successfully.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/207c2bfe-571f-4f9e-8815-92c38896631a)

After logging in, I finally got the user flag. (pwn3d! 🙂)

### Privilege escalation:

When I checked the groups of the "cry0l1t3", I observed that he is in the "adm" group. When searched the roles & responsibilities for this group I observed from this [doc](https://wiki.debian.org/SystemGroups): 

```
Group adm is used for system monitoring tasks. Members of this group can read many log files in /var/log, and can use xconsole.
Historically, /var/log was /usr/adm (and later /var/adm), thus the name of the group. 
```

