![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/566af2b4-6348-4358-b8cc-0aa972723d92)

https://app.hackthebox.com/machines/Keeper

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I started my enumeration with the quick rustscan to check for open ports & services running. After the scan is complete I only observed 2 open ports:

```bash
sudo rustscan -a 10.10.11.227 -- -sC -sV -vv -oN keeper_nmap
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d900b657-b4a4-45b2-ba7d-31e43f2a2853)

```Rust
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 35:39:d4:39:40:4b:1f:61:86:dd:7c:37:bb:4b:98:9e (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKHZRUyrg9VQfKeHHT6CZwCwu9YkJosNSLvDmPM9EC0iMgHj7URNWV3LjJ00gWvduIq7MfXOxzbfPAqvm2ahzTc=
|   256 1a:e9:72:be:8b:b1:05:d5:ef:fe:dd:80:d8:ef:c0:66 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBe5w35/5klFq1zo5vISwwbYSVy1Zzy+K9ZCt0px+goO
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Once the scan is complete & I found that port 80 is open & http server is running, I browsed the IP & observed a domain on the webpage.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/fd45cca8-24c2-4e23-9e8c-06e1ce6fc61f)

I added these domains "keeper.htb" & "tickets.keeper.htb" to my host config file.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Fuzzing:

Once I added the domain to the host config file I performed VHOST & Sub-directory enumeration on both the domains. But didn't found anything much useful.
Moving further I borwsed through the tickets domain & I observed a login panel:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/bc3c4e25-5480-43ae-b45b-0e3225408118)

I tried basic SQL injections but none of them wroked. I observed the page is running BestPractical application solution so I searched for the exploits but didn't found anything relavent as well.
Then I checked for the default credentials for the BestPractical logon & on this [page](https://www.192-168-1-1-ip.co/router/bestpractical/rt/12338/) I observed "root:password" as default credentials.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c8eb0517-d25c-4e7c-b5a5-ffeb456c2eb1)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

I tried the deafult credentials "root:password" & successfully logged into the BestPractical application.
After logging in I got the control panel access of the application as I logged in as a root user.

Moving further when I was enumerating through each & every option I observed a user under: Admin> Users > Select

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/011cf406-7117-4e4c-af22-cc56fcaafb77)

I observed a user "lnorgaard", when I clicked on the user details I found his credentials in the comments:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5617acae-9399-45a7-a668-da840ebf5073)

Using these credentials I logged in via SSH & successfully grabbed the user flag as well. (pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3c925115-6ad2-47c9-8483-5e7f7a44e864)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privilege Escalation:

Once I obtained the user flag I was looking for the possible vectors to escalate my privileges to gain root access. In the user folder it self I found a zip file "RT30000.zip", which transferred into my local kali host.
Once unzipped the file I obsereved 2 files from it:

- KeePassDumpFull.dmp
- passcodes.kdbx

Both of these files are from May 24 & looks like a dump from KeePass password manager. When checking for the exploits of the KeePass I found an article on [Hacker news](https://thehackernews.com/2023/05/keepass-exploit-allows-attackers-to.html) website.

According to the article, we can extract the clear text credentials from the KeePass by just using the memory dump.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/910db56a-fc13-4274-8d49-5c81d6be013b)

I also found a POC on the [Github](https://github.com/CMEPW/keepass-dump-masterkey.git) for CVE-2023-32784. I clone this password dumper into my kali host.

After cloning I executed the poc to get the passwords:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2e993cb4-c37a-47e7-8b4e-696ef6c06f60)

Once the dumping is completed I got the list of the possible passwords:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f2038a77-d937-4f6b-be58-9972631b0da7)

I opened the KeePass application, imported "passcode.kdbx" & then I used all the possible password one after another but none of them wroked. 😕
So I started searching the keywords on google where I observed a desert similar with the password pattern:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/dc7aa714-f89f-4c50-be69-560ed50b2821)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e90c57cf-d0f0-4934-8404-642d2c0e626c)

This time I used this new string to unlock the password file & it finally worked. 🙂
After the successfull import I looked through all the databases & found a putty private key in notes & password in it belongs to root user.

I tried to use the password but that didn't worked. Then the other option left is that I have to utilize Putty private key file to logon with SSH, which is in ".ppk" format currently.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/601e0544-c1e8-4e58-9d7f-12e909cf03b5)

So, to login with root user I have to convert the ".ppk" file in the Open-SSH format. To convert the file I installed putty & used putty agent to convert the file into open-ssh format:

```bash
sudo apt install putty

puttygen putty.ppk -O private-openssh -o id_rsa
```

Once the file is converted I used it to logon via SSH as root user & obtained the root flag. (pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/8d947ae9-a6b4-42f2-b6c1-b4b1d1f0ac44)

