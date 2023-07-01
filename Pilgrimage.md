  ![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/94ca46c3-f53b-47fb-8823-2dfdf6d0e413)
  https://app.hackthebox.com/machines/Pilgrimage

  ## Enumeration:

I started by performing a quick rustscan on the target to check for all open ports and running services. After completing the scan, I discovered 2 open ports:
```
Port 22: OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
Port 80: nginx 1.18.0
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0309b704-0c2f-493c-8881-8ccdeec91813)
```bash
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 20:be:60:d2:95:f6:28:c1:b7:e9:e8:17:06:f1:68:f3 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDnPDlM1cNfnBOJE71gEOCGeNORg5gzOK/TpVSXgMLa6Ub/7KPb1hVggIf4My+cbJVk74fKabFVscFgDHtwPkohPaDU8XHdoO03vU8H04T7eqUGj/I2iqyIHXQoSC4o8Jf5ljiQi7CxWWG2t0n09CPMkwdqfEJma7BGmDtCQcmbm36QKmUv6Kho7/LgsPJGBP1kAOgUHFfYN1TEAV6TJ09OaCanDlV/fYiG+JT1BJwX5kqpnEAK012876UFfvkJeqPYXvM0+M9mB7XGzspcXX0HMbvHKXz2HXdCdGSH59Uzvjl0dM+itIDReptkGUn43QTCpf2xJlL4EeZKZCcs/gu8jkuxXpo9lFVkqgswF/zAcxfksjytMiJcILg4Ca1VVMBs66ZHi5KOz8QedYM2lcLXJGKi+7zl3i8+adGTUzYYEvMQVwjXG0mPkHHSldstWMGwjXqQsPoQTclEI7XpdlRdjS6S/WXHixTmvXGTBhNXtrETn/fBw4uhJx4dLxNSJeM=
|   256 0e:b6:a6:a8:c9:9b:41:73:74:6e:70:18:0d:5f:e0:af (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBOaVAN4bg6zLU3rUMXOwsuYZ8yxLlkVTviJbdFijyp9fSTE6Dwm4e9pNI8MAWfPq0T0Za0pK0vX02ZjRcTgv3yg=
|   256 d1:4e:29:3c:70:86:69:b4:d7:2c:c8:0b:48:6e:98:04 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILGkCiJaVyn29/d2LSyMWelMlcrxKVZsCCgzm6JjcH1W
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-git: 
|   10.10.11.219:80/.git/
|     Git repository found!
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|_    Last commit message: Pilgrimage image shrinking service initial commit. # Please ...
| http-methods: 
|_  Supported Methods: GET HEAD POST
|_http-server-header: nginx/1.18.0
|_http-title: Pilgrimage - Shrink Your Images
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
In port 80, I observed that the server is running nginx version 1.18.0, and there is a domain present on that server: "**pilgrimage.htb**". Additionally, I noticed a './git' folder running on the host. I added the domain to my hosts file and browsed through it.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Web Enumeration:

Next, I performed subdomain and DNS host enumeration on the target host using GoBuster and FFUF.

In the results, I discovered 3 sub-directories:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/70ef7b65-79d7-4306-a4f5-386da3cddcbd)

However, the DNS host enumeration did not yield any significant findings. When I attempted to access the 3 directories, I encountered a 403 error, indicating the need for authentication before accessing them.

Moving on to the website itself, I found that it provides a tool for compressing image files:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0b8fdf7d-b468-4bbb-a1f6-f742c784d903)

After reviewing all the results, I proceeded to the GIT part and tried to directly access "http://pilgrimage.htb/.git/." Unfortunately, I couldn't access it. As a workaround, I utilized a tool called "git-dumper" to dump the GIT repository to my local system:

```bash
git-dumper http://pilgrimage.htb/.git/ git
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5aefc3c8-f744-4cee-a1eb-736b39b05c2e)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/eebb3178-7c76-4147-bbd8-275f6050133b)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### GIT enumeration:

After completing the dump, I proceeded with the GIT enumeration, which revealed the following:

- There was only one 'master' branch:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b01d41b3-a8ef-497b-9cda-6eb55ceaa398)

- Upon inspecting the GIT logs, I discovered a user associated with the commit ID e1a40beebc7035212efdcb15476f9c994e3634a7. The user was identified as emily emily@pilgrimage.htb.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4c4dbc9d-7d28-43c2-8329-43c04aba526f)

- I came across a PHP library named "BULLETPROOF" during my GIT exploration. This library is responsible for secure image uploads.
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9dd2cf3c-27ae-41f3-b9fc-71569613e823)

- Within the dumped GIT folder, I found a Linux executable file named "magic." It appears to be involved in image shrinking operations. The executable utilizes "ImageMagick 7.1.0-49," which is known to have a vulnerability that allows arbitrary file reading.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2e3cfcf2-4b39-4bcd-a4a8-990a29881351)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f5d4ca2c-0477-4105-9a1c-1a7f83093374)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

To exploit ImageMagick, I downloaded a proof-of-concept (POC) from this Github repository and followed the process outlined below.

First, I generated a malicious PNG file to read the contents of "/etc/passwd":

```python3
python3 generate.py -f "/etc/passwd" -o exploit.png
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/8d7cdd68-49fb-4c79-9870-8ff862c2e23b)

Next, I uploaded the file to the website to shrink it and downloaded the resulting PNG file to my attack box:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/fe657479-e11a-47a8-bc8f-9d7384e987e9)

Using "Exiftool," I extracted the raw content from the PNG file:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f1fc47be-8712-4b87-98a0-69cdbeaaf4c6)

I decoded this raw profile using the provided GitHub resource and obtained the contents of the "/etc/passwd" file:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d627f0d3-e840-40c2-8a20-7cebc80aa27b)

Having confirmed that the POC is functioning, I proceeded to gather more relevant information. Upon examining the "dashboard.php" file, I discovered that a SQL database was configured to verify user access:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2e231b0e-7885-4bf0-8431-c4103a4927a5)

I created a PNG image to fetch data from the "/var/db/pilgrimage" database, uploaded it, obtained the resulting image, and decoded it. As a result, I found a password belonging to **emily**.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b02bfce4-6289-41a3-a5f2-059c73d4f7ff)

Using the captured password, I attempted to log in via SSH and successfully gained access to the Emily profile, allowing me to retrieve the user flag. (pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c911a477-777d-49b8-a2b9-7afba77abad3)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privlege Escalation:

To search for privilege escalation vectors manually, I made the following observations:
```
- Emily may not run sudo on pilgrimage.
- No schedule jobs are running.
- No SUID/SGID Binaries, which would help in priv esc.
```

Next, I uploaded a pspy binary to check for running processes. During the process, I discovered a bash script named malwarescan.sh, which I assume is used to scan uploaded files on the website for malware.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/fa741d4d-4b8e-4c3b-bb04-4951ad922310)

The noteworthy aspect is that this file is owned by root, and we have read permissions for it.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/77f2e640-af9e-4ae7-ab92-5e3ba15083d4)

The content of the script is as follows:

```bash
#!/bin/bash

blacklist=("Executable script" "Microsoft executable")

/usr/bin/inotifywait -m -e create /var/www/pilgrimage.htb/shrunk/ | while read FILE; do
        filename="/var/www/pilgrimage.htb/shrunk/$(/usr/bin/echo "$FILE" | /usr/bin/tail -n 1 | /usr/bin/sed -n -e 's/^.*CREATE //p')"
        binout="$(/usr/local/bin/binwalk -e "$filename")"
        for banned in "${blacklist[@]}"; do
                if [[ "$binout" == *"$banned"* ]]; then
                        /usr/bin/rm "$filename"
                        break
                fi
        done
done
```

In the script, it's evident that the file is utilizing the "**binwalk**" binary.

Upon checking the version of binwalk, I discovered it's running version 2.3.2, which has a known exploit listed in Exploit-DB. This version of binwalk is vulnerable to remote code execution.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a1dbfd76-2118-448d-86f9-c690027fbdc8)

To summarize the findings, we can execute a command remotely using binwalk. When the malwarescan.sh script executes with root privileges, it will also execute binwalk with root privileges, allowing us to obtain a shell as the root user.

I downloaded the exploit and followed these steps to gain a shell on my host:

```
- created a PNG image file: image.png
- with the exploit I changed the file to run the netcat when binwalk is executed.
- When the malicious file is ready I uploaded it to the '/var/www/pilgrimage.htb/shrunk/' which I obsered that this is the location from where binwalk is performing the job.
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c304da05-307d-4837-a111-d80a094c1088)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/00870893-2f92-402b-a535-7a481695508c)

Upon uploading the malicious PNG file, I instantly received a connection back to my host, with the root user. (pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d028d091-567b-4b99-9e40-29c5ec18fd96)


