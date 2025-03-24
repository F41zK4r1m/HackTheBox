![image](https://github.com/user-attachments/assets/d4e32ee4-33ae-44ec-873b-bd3b54109306)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration:

## Port scan:

I began the enumeration with a quick `rustscan`, which revealed only two open ports: 22 and 80.

```
rustscan -a 10.10.11.47 -- -A -T4 -oN link_nmap
```

![image](https://github.com/user-attachments/assets/8681fab5-406e-4bf0-b35b-a5b8bdef2f18)

```
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:f8:b9:68:c8:eb:57:0f:cb:0b:47:b9:86:50:83:eb (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBMHm4UQPajtDjitK8Adg02NRYua67JghmS5m3E+yMq2gwZZJQ/3sIDezw2DVl9trh0gUedrzkqAAG1IMi17G/HA=
|   256 a2:ea:6e:e1:b6:d7:e7:c5:86:69:ce:ba:05:9e:38:13 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKKLjX3ghPjmmBL2iV1RCQV9QELEU+NF06nbXTqqj4dz
80/tcp open  http    syn-ack Apache httpd
|_http-server-header: Apache
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://linkvortex.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

From the scan results, I observed that an HTTP server is running on port 80, with the domain `linkvortex.htb`. I added this domain to my hosts configuration file.

## Web enumeration:

During the web application enumeration, I browsed the website and observed that it provides information about computer parts.

![image](https://github.com/user-attachments/assets/6533223d-266c-47fe-ad77-f0c0e6ba8829)

I inspected the HTML source code of the website but didn't find anything useful.

![image](https://github.com/user-attachments/assets/d12637a4-7e5d-4b43-a880-2f1aba624c3e)

Next, I performed subdirectory enumeration using dirsearch to identify any hidden or interesting directories. 

```
dirsearch -u http://linkvortex.htb/ -x 404,400
```

The results revealed a few directories associated with the domain.

![image](https://github.com/user-attachments/assets/eca180ae-6cb4-410f-bd6d-9b8905b2201b)

While enumerating the `robots.txt` file, I discovered additional directories that were not identified during the initial enumeration.

Upon browsing the `/ghost` directory, I encountered a Ghost CMS login page.

![image](https://github.com/user-attachments/assets/96372c93-9933-4722-a3a2-ec1f7a6df170)

Since it required authentication, I attempted several default credentials (e.g., admin:admin), but none of them worked.

## Sub-domain Enumeration:

To expand my attack surface, I performed sub-domain enumeration on the domain to identify any additional sub-domains. I utilized `FFUF` for this purpose:

```
ffuf -H "Host: FUZZ.linkvortex.htb" -u http://10.10.11.47/ -w /usr/share/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 230
```

During the scan, I discovered one sub-domain: `dev`.

![image](https://github.com/user-attachments/assets/62a860b5-f870-4dd0-8b3b-8443403e57ac)

I added this sub-domain to the hosts configuration file and browsed to it. The website displayed a "**Launching Soon**" page.

![image](https://github.com/user-attachments/assets/13eddc35-aac5-4a08-b349-9024828321f4)

Since the website contained no visible data, I performed sub-directory enumeration using dirsearch. This time, I discovered a `/.git` directory.

```
dirsearch -u http://dev.linkvortex.htb/
```

![image](https://github.com/user-attachments/assets/e25bb563-d04f-41c4-bd27-593e542fe491)

## Git Enumeration:

Upon discovering the `.git` directory, I utilized a tool called `git-dumper` to dump the repository locally.

```
git-dumper http://dev.linkvortex.htb/.git/ git
```

![image](https://github.com/user-attachments/assets/27b9d758-1a18-4994-84e1-74cf0d4ef2c7)

Once the Git repository was dumped, I navigated into the `.git` directory and checked the `git log`, where I found several commits and usernames associated with them:

```
git log --raw
```

![image](https://github.com/user-attachments/assets/96bdc922-f0c7-458d-856f-c62dfe2d475e)

I thoroughly analyzed the commits, but didn't find anything sensitive.

![image](https://github.com/user-attachments/assets/c149ca07-e2dc-449f-9174-74ee9f09a0e9)

After conducting some research, I decided to use another tool called [GitHack](https://github.com/lijiejie/GitHack), which rebuilds the source code from the `.git` directory while preserving the original directory structure.

![image](https://github.com/user-attachments/assets/8f61dd2b-f999-4f69-b938-622dc5745e5d)

Using `GitHack`, I rebuilt the project files on my attack machine:

```
python3 GitHack.py http://dev.linkvortex.htb/.git/
```

The process took some time but successfully generated numerous files. Upon enumerating through the files and grepping for the keyword password, I discovered credentials in the following file:

```
/ghost/core/test/regression/api/admin/authentication.test.js
```
![image](https://github.com/user-attachments/assets/e5de3795-67be-437a-8073-6104812c3214)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Initial access

## Exploitation:

After obtaining the credentials, I attempted to use them against the Ghost CMS login portal, but none of the user credentials worked. However, when I tried the `admin@linkvortex.htb` email, it successfully authenticated with one of the discovered passwords.

![image](https://github.com/user-attachments/assets/861e6bb8-5884-4a9f-a9fa-0bda9bcfd037)

![image](https://github.com/user-attachments/assets/4a3efd5b-4fa1-4802-8aa4-0c64c53c08e6)

After logging in, I explored the application's functionalities in an attempt to gain Remote Code Execution (RCE), but didn't find any obvious vulnerabilities.

I then used Wappalyzer to identify the technology stack, where I observed that the Ghost CMS version was `5.58`.

![image](https://github.com/user-attachments/assets/3df8b6b8-cc00-465e-8329-7e68cb212d12)

After conducting some research, I found that version 5.58 is vulnerable to Arbitrary File Read, as documented on [Snyk](https://security.snyk.io/package/npm/ghost/5.58.0):

```
Affected versions of this package are vulnerable to Arbitrary File Read which allows authenticated users to upload files that are symlinks. This can be exploited to perform an arbitrary file read of any file on the host operating system.
```

I located a working exploit on [GitHub](https://github.com/0xyassine/CVE-2023-40028) for this version, which I used to exploit the vulnerability.

I cloned the exploit repository, reviewed the code, modified the URL and credentials, and then executed the exploit.

![image](https://github.com/user-attachments/assets/142f6ce7-6aa3-41f9-a3e8-402a3ff61956)

![image](https://github.com/user-attachments/assets/4158f246-d9dc-40c6-bdfa-525cb3ad06d8)

## SSH access:

Although I was able to read files from the host, I still didn't have valid credentials and was unsure where to look for them.

So, I decided to revisit the GitLeak folder and analyze the `Dockerfile.ghost` file. Upon inspecting the file, I found several configuration file locations that seemed interesting.

![image](https://github.com/user-attachments/assets/e3bae1c5-9416-4db1-a178-03199ca8a28f)

I proceeded to analyze the configuration file, where I discovered SMTP credentials.

![image](https://github.com/user-attachments/assets/19e5e61e-096a-4b46-91f9-403eefd69e9c)

Out of curiosity, I tried using the same credentials for SSH authentication and surprisingly, it worked!

I successfully logged in as the user `bob` over SSH and was able to retrieve the user flag.

![image](https://github.com/user-attachments/assets/b0a5302f-fe40-4216-bfbc-96eaf8502a16)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Privilege Escalation:

## Sudo Privileges:

After gaining access as the user `Bob`, I began enumerating the system to escalate my privileges to root.

I started by checking sudo privileges, where I observed that Bob could execute the following command as the root user without requiring a password:

```
Matching Defaults entries for bob on linkvortex:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty, env_keep+=CHECK_CONTENT

User bob may run the following commands on linkvortex:
    (ALL) NOPASSWD: /usr/bin/bash /opt/ghost/clean_symlink.sh *.png
```

![image](https://github.com/user-attachments/assets/02bdfa21-e9e6-484d-8922-f2e5fdd53893)

Next, I analyzed the bash script `/opt/ghost/clean_symlink.sh` and found the following content inside:

![image](https://github.com/user-attachments/assets/0891efaf-8c5a-4d36-8fe2-304d1fa2f09b)

### Script Analysis:

- The script is designed to move PNG symlink files into the /var/quarantined directory.
- By default, the script sets CHECK_CONTENT=false, meaning it doesn't display the content of the file.
- However, if the CHECK_CONTENT environment variable is set to true, the script will print the content of the file.


## Root access:

To exploit this misconfiguration, I created a symbolic link (`bob.txt`) pointing to the root user's SSH private key (`/root/.ssh/id_rsa`):

```
ln -s /root/.ssh/id_rsa bob.txt
```

Next, I created another symbolic link (`bob.png`), pointing to `bob.txt`, effectively chaining the symlink to the SSH private key:

```
ln -s /home/bob/bob.txt bob.png
```

After setting up the links, I executed the `clean_symlink.sh` script with sudo privileges, but nothing was displayed in the output:

![image](https://github.com/user-attachments/assets/edfdf5d6-454c-429b-9403-005e65a938db)

To bypass this, I re-ran the script with `CHECK_CONTENT=true`, which forced it to print the contents of the file. This time, I successfully retrieved the root SSH private key:

![image](https://github.com/user-attachments/assets/11fa755e-9f0d-4a9c-b0af-f40df5d790db)

Using the extracted private key, I logged in as the root user via SSH and finally captured the root flag. (pwn3d!🎉)

![image](https://github.com/user-attachments/assets/7704e89f-3762-451a-94ba-9a384232ecbe)
