  ![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/94ca46c3-f53b-47fb-8823-2dfdf6d0e413)
  https://app.hackthebox.com/machines/Pilgrimage

  ## Enumeration:

I started with performing the quick rustscan on the target to check all open ports & running services. After the completion of the scan I found 2 open ports:

  - 22 -> OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
  - 80 -> nginx 1.18.0

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
In the port 80, I observed it's running the nginx server with version 1.18.0 & there is a domain present on that server: "pilgrimage.htb". Along with that I also observed './git' folder running on the host.
I added the domain to my hosts file & browsed through it.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Web Enumeration:

I then performed subdomain & DNS host enumeration on the target host, using GoBuster & FFUF.

I observed 3 sub-directories in the results:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/70ef7b65-79d7-4306-a4f5-386da3cddcbd)

Didn't found anything in the DNS host enumeration. Also, when tried to access those 3 directories but got 403 error which looks like I need to authenticate before accessing the directories.

Then I checked the website & found it's providig tool to compress image files.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0b8fdf7d-b468-4bbb-a1f6-f742c784d903)

After going through all the results I move onto the GIT part & tried to accessed it but it seems like I can't access "http://pilgrimage.htb/.git/" directly. So, I used a tool called "git-dumper" to dump the GIT repo into my local system.

```bash
git-dumper http://pilgrimage.htb/.git/ git
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5aefc3c8-f744-4cee-a1eb-736b39b05c2e)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/eebb3178-7c76-4147-bbd8-275f6050133b)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### GIT enumeration:

After the dump I started with the GIT enumeration:

- Found just one 'master' branch:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b01d41b3-a8ef-497b-9cda-6eb55ceaa398)

- In GIT logs I found a user with the commit ID: **e1a40beebc7035212efdcb15476f9c994e3634a7**, user: emily <emily@pilgrimage.htb>

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4c4dbc9d-7d28-43c2-8329-43c04aba526f)

- I found a PHP library "BULLETPROOF", which handles secure image uploads.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9dd2cf3c-27ae-41f3-b9fc-71569613e823)

- In the dumped GIT folder I found a linux executable file name: "magic", which looks like performing the operation of image shrink. It's using "ImageMagick 7.1.0-49", which is vulnerable to arbitrary file read.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2e3cfcf2-4b39-4bcd-a4a8-990a29881351)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f5d4ca2c-0477-4105-9a1c-1a7f83093374)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

To exploit the ImageMagic I downloaded a POC from this [Github repo](https://github.com/Sybil-Scan/imagemagick-lfi-poc) & followed the process.

First I generated a malicious PNG file to read /etc/passwd content:

```python3
python3 generate.py -f "/etc/passwd" -o exploit.png
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/8d7cdd68-49fb-4c79-9870-8ff862c2e23b)

Then uploaded the file to website for shrinking & download the result PNG file to my attack box.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/fe657479-e11a-47a8-bc8f-9d7384e987e9)

Using the "Exiftool" I extracted the RAW content of the PNG file:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f1fc47be-8712-4b87-98a0-69cdbeaaf4c6)

Decoded this raw profile in the GitHub & got the result of '/etc/passwd' file content:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d627f0d3-e840-40c2-8a20-7cebc80aa27b)

Now, as I confirmed that the POC is working, I moved on to fetch more relevant information & observed in the dashboard.php file that there is a SQL database configured which is verifying the user access:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2e231b0e-7885-4bf0-8431-c4103a4927a5)

I created a PNG image to fetch the data from DB "/var/db/pilgrimage" & uploaded it, got the result image & after decoding the image I found a password which belongs to **emily**.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b02bfce4-6289-41a3-a5f2-059c73d4f7ff)

Used the grabbed password & tried to logged in via SSH. Using the password I successfully logged into the Emily profile & grabbed the user flag.(pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c911a477-777d-49b8-a2b9-7afba77abad3)
