![image](https://github.com/user-attachments/assets/cd998581-3a08-4cb5-a5be-4200d8b19a37)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration

## Port & service scan:

I started the enumeration process by scanning for open ports and running services using `rustscan`. The results revealed only two open ports:

```
rustscan -a 10.10.11.18 -- -A -T4 -vv -oN usage_nmap
```

![image](https://github.com/user-attachments/assets/c10abd6e-6de3-4304-b4c9-d97459770e03)

```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 a0:f8:fd:d3:04:b8:07:a0:63:dd:37:df:d7:ee:ca:78 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBFfdLKVCM7tItpTAWFFy6gTlaOXOkNbeGIN9+NQMn89HkDBG3W3XDQDyM5JAYDlvDpngF58j/WrZkZw0rS6YqS0=
|   256 bd:22:f5:28:77:27:fb:65:ba:f6:fd:2f:10:c7:82:8f (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHr8ATPpxGtqlj8B7z2Lh7GrZVTSsLb6MkU3laICZlTk
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://usage.htb/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Linux 5.0 - 5.5 (96%), Linux 5.0 (96%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), Linux 4.15 - 5.8 (94%), Linux 5.3 - 5.4 (94%), Linux 2.6.32 (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=10/1%OT=22%CT=%CU=31931%PV=Y%DS=2%DC=T%G=N%TM=66FBA814%P=x86_64-pc-linux-gnu)
SEQ(SP=F2%GCD=1%ISR=111%TI=Z%CI=Z%II=I%TS=A)
SEQ(SP=F4%GCD=1%ISR=112%TI=Z%CI=Z%II=I%TS=A)
OPS(O1=M53CST11NW7%O2=M53CST11NW7%O3=M53CNNT11NW7%O4=M53CST11NW7%O5=M53CST11NW7%O6=M53CST11)
WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)
ECN(R=Y%DF=Y%T=40%W=FAF0%O=M53CNNSNW7%CC=Y%Q=)
T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)
IE(R=Y%DFI=N%T=40%CD=S)
```

From the scan results, I observed that ports 22 and 80 were open. Additionally, there was an HTTP server running on port 80, where I discovered the domain `usage.htb`. I added this domain to my hosts configuration file.

## DNS enumeration:

Next, I performed DNS-based enumeration on the domain `usage.htb` to search for additional subdomains. For this, I used the tool `ffuf`.

```
ffuf -H "Host: FUZZ.usage.htb" -u http://10.10.11.18/ -w /usr/share/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 178
```

As a result of the scan, I identified one new subdomain: `admin.usage.htb`.

![image](https://github.com/user-attachments/assets/8bfab298-ae7b-47bb-8fce-d7d1e5d136cb)

## Web enumeration:

Next, I explored the web applications and found login portals on both websites.

For `usage.htb`:

![image](https://github.com/user-attachments/assets/72586e37-3094-4f8e-8813-4ed324d3ca88)

For `admin.usage.htb`:

![image](https://github.com/user-attachments/assets/eac061c5-5803-4a80-a26f-ef7b9a617da7)

I checked the HTML source code for both websites but didn't find anything useful.

## sub-directory enumeration:

Next, I conducted subdirectory enumeration on both hosts using the tool `dirsearch`:

![image](https://github.com/user-attachments/assets/90222702-0281-4fe1-835e-2c6292747166)

![image](https://github.com/user-attachments/assets/4c2d2465-1dc5-4de1-9e44-cbd0c2838405)

However, the scan results didn't reveal anything particularly useful. So, I registered an account and logged into the `usage.htb` application. After logging in, I found a page containing several blog posts:

![image](https://github.com/user-attachments/assets/81516b15-58dc-4309-8406-bfaf9a27dec7)

## Password resest:

After reviewing everything, I began testing the password reset functionality. I intercepted the password reset request using Burp Suite and noticed that the application was using a `laravel_session` cookie, indicating that it is running on PHP.

![image](https://github.com/user-attachments/assets/60fba018-a744-45e2-b0d6-f6d13fadecfc)

Next, I attempted an SQL injection (SQLi) in the password reset field by inserting a single quote after the email ID. This resulted in a `500 Internal Server Error`.

![image](https://github.com/user-attachments/assets/8959cd52-5f40-47fe-99ae-9647dec46229)

To verify if the error was caused by SQLi, I tried using two single quotes, and this time the application responded normally. This confirmed that the application is vulnerable to SQL injection.

![image](https://github.com/user-attachments/assets/605bfa02-b398-4cc8-8af6-27eeb6742585)

## SQLMap:

To further exploit the vulnerability and dump all the data from the database, I used `sqlmap` to automate the process.

I saved the password reset request intercepted in Burp Suite to a text file and used it as input for `sqlmap`:

```
sqlmap -r burp.req -p email --level=5 --risk=3 --batch --dbs --dump
```

The command revealed three available databases:

```
[*] information_schema
[*] performance_schema
[*] usage_blog
```

![image](https://github.com/user-attachments/assets/93383878-919c-49db-bca6-546b00565a69)

Since the `usage_blog` database seemed related to the website's blog, I decided to dump its contents:

```
sqlmap -r burp.req -p email --level=5 --risk=3 --batch --dump usage_blog --thread 10  #thread 10 has been used to speed up the process
```

After some time, I began retrieving valuable data, such as the administrator's password hash and token:

![image](https://github.com/user-attachments/assets/ca1dc6bf-9098-46bd-9bf3-88ade169b787)

Additionally, I retrieved user hashes and tokens:

![image](https://github.com/user-attachments/assets/404cf018-22b3-40c2-90dc-0b0259fd0a46)

## Password cracking:

Next, I analyzed the password hash algorithm using the tool `hashid`, which revealed that the hash was in `bcrypt` format. I used `John the Ripper` to crack these hashes:

```
john --w=/usr/share/wordlists/rockyou.txt --format=bcrypt hashes
```

Within two minutes, I successfully cracked all three hashes:

![image](https://github.com/user-attachments/assets/0393a7cc-925c-4afc-8448-d0dd4b823efb)

One of the three credentials worked for the user `admin`, allowing me to successfully log in to the admin portal.

![image](https://github.com/user-attachments/assets/99b77488-7171-4d0f-9a12-62e34cbd50fa)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Initial access

## PHP shell:

Once I accessed the dashboard, I noticed that the application was running Laravel-admin version 1.8.18. I began searching for an exploit and found relevant information on [Snyk](https://security.snyk.io/vuln/SNYK-PHP-ENCORELARAVELADMIN-3333096). According to Snyk, "Affected versions of this package are vulnerable to Arbitrary Code Execution due to unrestricted file uploads via the 'user settings' interface. Users can upload and execute `.php` scripts on the affected server."

In the application, I observed a file upload feature in the profile image section:

![image](https://github.com/user-attachments/assets/a7e9ce15-dfaa-4c12-9989-52bc685f8665)

I attempted to upload a web shell directly, but it was blocked, likely due to the file extension. I tried again, this time intercepting the request using Burp Suite and modifying the file extension during the request:

![image](https://github.com/user-attachments/assets/588c850c-a231-41d1-9ae1-929124377c03)

This worked, and the image file was successfully uploaded as `shell.php`:

![image](https://github.com/user-attachments/assets/491865ce-afb3-4009-9ca0-ce8203808e88)

Next, I navigated to the uploaded shell page and executed commands, which worked as expected:

![image](https://github.com/user-attachments/assets/98d7ab7c-eeee-46a0-a673-e6424fa424c5)

## SSH keys:

Using the web shell, I navigated to the home directory and found two user folders: `dash` and `xander`.

![image](https://github.com/user-attachments/assets/0971e8a8-2ce2-46f0-976b-2ff5febee046)

Since the shell was running under the `dash` user, I explored the directory and discovered a private SSH key in `dash's` home folder.

![image](https://github.com/user-attachments/assets/83b88a7d-1151-4bd2-a50e-56cfda07cf90)

I copied the private key and attempted to log in as `dash` using SSH, which worked, giving me access to the `dash` account:🙂

![image](https://github.com/user-attachments/assets/52da43f6-ef69-411d-a456-45d060336ddf)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# lateral movement:

## dash -> xander:

After gaining initial access, I began searching for ways to escalate my privileges. I started by examining the files in `dash's` home directory and noticed several `monit` files.

```
Monit is a small Open Source utility for managing and monitoring Unix systems.
```

Upon checking the contents of one of these files, I discovered credentials for the Monit application:

![image](https://github.com/user-attachments/assets/df05e5d4-1215-4743-9fec-849f1adb7574)

I first attempted to use these credentials with the root user, but that didn’t work. Then, I tried them with the other user, `xander`, and this time it was successful.

## xander -> root:

After gaining access to `xander`, I continued to search for ways to escalate my privileges. I started by checking the sudo privileges and observed that `xander` can run the following command with sudo:

```
sudo -l

Matching Defaults entries for xander on usage:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User xander may run the following commands on usage:
    (ALL : ALL) NOPASSWD: /usr/bin/usage_management
```
I executed the binary directly, which presented me with three options:

![image](https://github.com/user-attachments/assets/e687fed4-5c48-4362-a401-07d8545b4115)

To further analyze the binary, I used the `strings` command and noticed that in the backup section, the application was creating backups from the `www/html` directory after zipping them using 7-Zip:

![image](https://github.com/user-attachments/assets/58fabd39-a548-470c-8394-a03a5d97cddd)

I researched some references and found a post on [HackTricks](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/wildcards-spare-tricks#id-7z), which explained how to abuse 7-Zip for privilege escalation if wildcards are used in the command:

![image](https://github.com/user-attachments/assets/726a51da-5efb-46c9-be29-47c3e7242093)

## Root.txt

Following the guidance from the HackTricks blog, I created a file named `id_rsa` in the `/var/www/html` directory, where backups are being performed with a wildcard. After creating this file, I set it up as a symlink for the SSH private key for the root user.

![image](https://github.com/user-attachments/assets/3ea0909a-29e4-4f5e-9605-14ce596c2158)

After running the `user_management` binary with sudo again, I successfully obtained the root user SSH key:

![image](https://github.com/user-attachments/assets/a686d1b6-8950-459d-9858-b9ca9f1b225b)

Essentially, I created two files: `id_rsa` and `@id_rsa`. The `@id_rsa` file served as a reference to the `id_rsa` file, and since I made `id_rsa` a symlink to the root SSH key, it ultimately allowed me to read the SSH key for the root user.

Using this key, I was finally able to access the root account and fetch all the flags. (pwn3d! 🎉)

![image](https://github.com/user-attachments/assets/10593e87-0e56-48fc-a42e-ce1f1c4c3c5f)


