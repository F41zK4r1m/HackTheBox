  ![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5860a245-8aa8-48a6-8582-b5b85d72b670)
  https://app.hackthebox.com/machines/PC

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I began the enumeration with the quick Rsutscan on the target machine but I only got 2 ports open, 22 & 50051:

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

I visited the webpage on port 50051 but I observed that it is runnig some kind of software other than web application:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5d4d910a-fa88-4dba-8cd9-038adb2ea9f9)

I tried to connect to the port via netcat but the conection is getting dropped quickly after running any of the command:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/6788ec89-3e99-49f0-bd09-338eee2ef52b)

Then after multiple attempts to connect via port 50051 I searched for the port service running on 50051 & I found'gRPC' service is running on 50051.

```
gRPC is designed to work with a variety of authentication mechanisms, making it easy to safely use gRPC to talk to other systems. You can use our supported mechanisms - SSL/TLS with or without Google token-based authentication - or you can plug in your own authentication system by extending our provided code.

gRPC also provides a simple authentication API that lets you provide all the necessary authentication information as Credentials when creating a channel or making a call.
```

I got this [blog](https://medium.com/@ibm_ptc_security/grpc-security-series-part-3-c92f3b687dd9) on medium where the full exploitation process of gRPC is explained.

So, I have to make connection on gRPC service & to perform the connection I downloaded 'grpccurl' from this [Github repo](https://github.com/fullstorydev/grpcurl/releases)

```bash
grpcurl -plaintext 10.10.11.214:50051 list
```

I got 2 services listed:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2242e9ae-952c-4199-aea6-b7f75155869f)

I decided to enumerate the SimpleApp service & listed it's modules:

```bash
grpcurl -plaintext 10.10.11.214:50051 list SimpleApp
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/7939cc35-2ff8-4c63-9746-131b0ba911e1)

I got 3 modules in the SimpleApp:
```
SimpleApp.LoginUser
SimpleApp.RegisterUser
SimpleApp.getInfo
```

I enumerated all 3 services & it seems like I need to have an account/authentication token to use the service:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/53109ad4-d31b-4ac2-b1ce-93745658dbba)

I downloaded grpcui that will initiate a UI portal on which we can provide our data to create or modify our blog. There are other operations also which we can use from this portal.

```bash
grpcui -plaintext 10.10.11.214:50051
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d7d2eeb9-a203-4eea-b52e-1a11828483cc)

I got the UI page opened in my browser directly after running the command:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2ba0c48b-5861-4fd5-ae5a-343559ad28c2)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Initial access:

In the LoginUser portal I tried the default 'admin:admin' credentials & got successfully logged-in.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/269a897f-887b-4c5c-9c04-3481474a5246)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/627a58bb-f7de-4444-b88c-5901bdd9e6a4)

I got the token & ID of the administrator which I used it to get info:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/02f99a8e-1b4d-458c-8c8d-544296765c42)

I sent the request to Burp & save the request part:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3f8f7236-0954-42d3-92a2-1348fe00d506)

With the saved requested part I ran SQLmap to retrieve the more info from DB & finally got the user & password:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2a31f627-cb8a-4ad5-affd-3da6539c1b6d)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/38b2e9af-2709-406a-baac-555b5e9d059a)

With the credentials dumped from the DB I tried to login via SSH & finally able to login successfully, where I got the user flag as well: (pwn3d!)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/7719660a-b032-49bc-999f-44a5ab1add2b)














