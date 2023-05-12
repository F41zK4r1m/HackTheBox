  ![image](https://user-images.githubusercontent.com/87700008/236646161-75eb7f04-736d-40bd-b071-2bfd16e50a42.png)
https://app.hackthebox.com/machines/539

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

Started with rustscan & found 2 open port: 22 & 80

```
sudo rustscan -a 10.10.11.211 -- -sC -sV -vv -oN monitor2_nmap
```

![image](https://user-images.githubusercontent.com/87700008/236646587-e8465f10-58df-45b9-9bb0-cfabc39556d0.png)

```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC82vTuN1hMqiqUfN+Lwih4g8rSJjaMjDQdhfdT8vEQ67urtQIyPszlNtkCDn6MNcBfibD/7Zz4r8lr1iNe/Afk6LJqTt3OWewzS2a1TpCrEbvoileYAl/Feya5PfbZ8mv77+MWEA+kT0pAw1xW9bpkhYCGkJQm9OYdcsEEg1i+kQ/ng3+GaFrGJjxqYaW1LXyXN1f7j9xG2f27rKEZoRO/9HOH9Y+5ru184QQXjW/ir+lEJ7xTwQA5U1GOW1m/AgpHIfI5j9aDfT/r4QMe+au+2yPotnOGBBJBz3ef+fQzj/Cq7OGRR96ZBfJ3i00B/Waw/RI19qd7+ybNXF/gBzptEYXujySQZSu92Dwi23itxJBolE6hpQ2uYVA8VBlF0KXESt3ZJVWSAsU3oguNCXtY7krjqPe6BZRy+lrbeska1bIGPZrqLEgptpKhz14UaOcH9/vpMYFdSKr24aMXvZBDK1GJg50yihZx8I9I367z0my8E89+TnjGFY2QTzxmbmU=
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBH2y17GUe6keBxOcBGNkWsliFwTRwUtQB3NXEhTAFLziGDfCgBV7B9Hp6GQMPGQXqMk7nnveA8vUz0D7ug5n04A=
|   256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKfXa+OM5/utlol5mJajysEsV4zb/L0BJ1lKxMPadPvR
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-favicon: Unknown favicon MD5: 4F12CCCD3C42A4A478F067337FE92794
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Login to Cacti
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
I checked the website & found that on port 80 there is a Cacti service running, which is an open-source, web-based monitoring solution.
![image](https://user-images.githubusercontent.com/87700008/237025268-926927c5-8cd0-433a-a233-fa4b02ab9e07.png)

I tried to bypass the authentication using SQLi but didn't succedded, I checked HTML source code of the website but didn't found anything helpful, then I scanned for the sub-directories:

```
ffuf -u http://10.10.11.211/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

In the results I found multiple directories but all of them requires authentication.

![image](https://user-images.githubusercontent.com/87700008/237025773-641cede3-abb7-4528-afca-14eb333c730b.png)

In the webpage I can clearly see the version of cacti is 1.22, so I started searching for the vulnerability. I found out that Cacti 1.22 is vulnerable, CVE-2022-46169 with a CVSS score of 9.8.
It's vulnerable to RCE. Unauthenticated attackers could exploit a vulnerable Cacti instance if any monitored device uses a specific data source. Exploiting allows attackers to run arbitrary commands under the same user as the web server process is running.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Initial access:

I found this exploit in this [Github repo](https://github.com/FredBrave/CVE-2022-46169-CACTI-1.2.22), which I downloaded it into my attacking machine.
After the download I executed the exploit & quickly got the reverse shell in my netcat listener, with the user running as 'www-data'. 🙂

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0490e050-571d-4249-b2d1-a756c22efed3)

After some enumeration I observed that currently I am in the docker environment:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/96904852-b175-42da-aca1-9b8e5be25539)

In the '/' folder I noticed a file called 'entrypoint.sh' for which I am having the read access. By checking file I found this code:

```
#!/bin/bash
set -ex

wait-for-it db:3306 -t 300 -- echo "database is connected"
if [[ ! $(mysql --host=db --user=root --password=root cacti -e "show tables") =~ "automation_devices" ]]; then
    mysql --host=db --user=root --password=root cacti < /var/www/html/cacti.sql
    mysql --host=db --user=root --password=root cacti -e "UPDATE user_auth SET must_change_password='' WHERE username = 'admin'"
    mysql --host=db --user=root --password=root cacti -e "SET GLOBAL time_zone = 'UTC'"
fi

chown www-data:www-data -R /var/www/html
# first arg is `-f` or `--some-option`
if [ "${1#-}" != "$1" ]; then
        set -- apache2-foreground "$@"
fi

exec "$@"
```

As per the code I checked the tables in cacti DB with root credentials & found a lot of tables:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9c583bff-30a1-4e3f-9531-64e71b4d7f2f)

I observed 1 interseting table 'user_auth' & fetched the details of that table:

```
mysql --host=db --user=root --password=root cacti -e "select * from user_auth"
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/de596dd2-62c3-4279-acc7-2b81560e2ace)

In the table I observed 3 users "admin", "guest" & "marcus" with their hashed passwords.

At this point of time I added the "monitorstwo.htb" to the hosts file as the domain is visible in database & checked the hashing algorithm of Marcus as well & it seems like the hash is in bcrypt format:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3deb5d51-24cc-415c-872d-0312955f2c19)

I used HashCat to crack the hash with mode 3200 & in few seconds I got the clear text password :

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/eeeff081-b9bb-4008-b49a-1f1594be765f)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### User.txt:

Now, with the cracked credentials I tried to login via SSH & successfully logged into Marcus account.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/73c26538-b1bf-42f3-9183-7de37832a46c)

Also, got the user flag as well. (pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a69a5718-3ba1-40bc-8703-d9291e9238ba)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Priv Esc:

I started checking with the manual enumeration like:

  - checking sudo privileges
  - checking cron jobs
  - checking SUID binaries

But in all of them I didn't found anythinh helpful as Marcus is not allowed to run the program as sudo, as he is not in the sudoers list. 😕

Then I tarnsferred the Linpeas binary to enumerate further for the privilge escalation.
After running the Linpeas I found an intresting folder which belongs to Marcus, which contains a mail from security team.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/301db084-cbce-44ff-8aba-40cc24d3e6c8)

In the mail I found 3 vulnerabilities where I observed a vulnerability "CVE-2021-41091":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/320817d7-9ea6-4d47-abc3-68292492ed41)

```
CVE-2021-41091, the overly permissive directory permissions in /var/lib/docker/overlay2 enable unprivileged users to access and execute programs within the containers, leading to a potential privilege escalation attack.
In short we can execute the file from containers into the host machine with the same file permission of the container, which means if we have created any file with root permissions in the container then we can execute the same file with root privileges without having the root access.
```

For this we have to escalate our privilege in the container environment as well. And for this I again executed Linpeas in the container.
After running the Linpeas I found "capsh" setuid, which we can use to escalate privilege:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/653b1352-4b4c-4a12-99f0-32c847e9fa68)

I executed the below command as 'www-data' & quickly got the root shell:

```
capsh --gid=0 --uid=0 --
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c994f6d6-029e-4cb2-a140-daf8027f39de)

After having the root shell in the container I created a root bash in the /tmp folder:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b0806c37-a7fb-4e15-9744-97eb1e5a9e24)

As per the full vulnerability description :

```
The overlay filesystem is a critical component in exploiting this vulnerability. Docker's overlay filesystem enables the container's file system to be layered on top of the host's file system, thus allowing the host system to access and manipulate the files within the container. In the case of CVE-2021-41091, the overly permissive directory permissions in /var/lib/docker/overlay2 enable unprivileged users to access and execute programs within the containers, leading to a potential privilege escalation attack.
```

I used 'findmnt' to search for overlay filesystem:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a92c43b3-458c-4152-85dd-584587d011ca)

I then checked if my SUID bash is visible from the host machine or not, which I found visible:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0c9aea7b-cbcc-443d-af85-549c62db54c1)

Executed the 'bash -p' & got the root shell quickly: (pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5decf271-bc1c-4143-b4a4-427376f158f9)
