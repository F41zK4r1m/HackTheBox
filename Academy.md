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
