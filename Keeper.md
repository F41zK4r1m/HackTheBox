![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/566af2b4-6348-4358-b8cc-0aa972723d92)

https://app.hackthebox.com/machines/Keeper

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I initiated the enumeration process by utilizing the "rustscan" tool to identify open ports and active services. Following the scan, I was able to identify two active ports:

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

Upon completion of the scan, my attention was drawn to port 80, where an HTTP server was detected. Prompted by this discovery, I accessed the IP through a web browser, revealing a domain on the displayed webpage.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/fd45cca8-24c2-4e23-9e8c-06e1ce6fc61f)

To facilitate further exploration, I appended the domains "keeper.htb" and "tickets.keeper.htb" to my local host configuration file. 

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Fuzzing:

With the domains properly configured in my host file, I moved on to performing Virtual Host (VHOST) and sub-directory enumeration on both "keeper.htb" and "tickets.keeper.htb." However, the results did not yield any substantial leads.

My focus then shifted to the "tickets.keeper.htb" domain, where I encountered a login panel:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/bc3c4e25-5480-43ae-b45b-0e3225408118)

In my attempt to exploit this panel, I experimented with basic SQL injection techniques, but none of them proved successful. Recognizing that the page operated on the BestPractical application solution, I began searching for relevant exploits. Regrettably, I didn't come across any immediately applicable exploits.

Continuing my investigation, I decided to explore default login credentials for BestPractical. My search led me to a [webpage](https://www.192-168-1-1-ip.co/router/bestpractical/rt/12338/) where I uncovered default login credentials: "root:password."

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c8eb0517-d25c-4e7c-b5a5-ffeb456c2eb1)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

Utilizing the default credentials "root:password," I gained access to the BestPractical application. This allowed me to infiltrate the application's control panel, essentially assuming the role of a root user within the platform.

As I delved into the various options available, I stumbled upon a significant discovery. Navigating to **Admin > Users > Select**, I encountered a user profile for "lnorgaard."

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/011cf406-7117-4e4c-af22-cc56fcaafb77)

More notably, within the comments section of this user's details, I located a set of credentials:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5617acae-9399-45a7-a668-da840ebf5073)

Leveraging these newfound credentials, I initiated an SSH login, successfully establishing a connection using "lnorgaard" as the user. This accomplishment marked a major milestone as I managed to procure the user flag during this phase of the engagement. (Pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3c925115-6ad2-47c9-8483-5e7f7a44e864)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privilege Escalation:

Having successfully obtained the user flag, my focus shifted towards identifying potential avenues for privilege escalation, ultimately aiming to attain root access. Within the user's directory, I stumbled upon a compressed file named "RT30000.zip," which I promptly transferred to my local Kali host.

Upon extraction, two files greeted me:

- KeePassDumpFull.dmp
- passcodes.kdbx

Both files were last modified on May 24 and appeared to be related to a KeePass password manager dump. In my search for relevant exploits regarding KeePass, I came across an article on [Hacker News](https://thehackernews.com/2023/05/keepass-exploit-allows-attackers-to.html).

The article detailed a method to extract clear text credentials from KeePass using a memory dump, underscoring the significance of the discovery:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/910db56a-fc13-4274-8d49-5c81d6be013b)

Additionally, I chanced upon a Proof of Concept (POC) for CVE-2023-32784 on [GitHub](https://github.com/CMEPW/keepass-dump-masterkey.git). Intrigued by the possibilities, I cloned the repository to my Kali host and executed the POC to initiate the password extraction process:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2e993cb4-c37a-47e7-8b4e-696ef6c06f60)

Upon completion, the dumped passwords were revealed:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f2038a77-d937-4f6b-be58-9972631b0da7)

Upon obtaining the password list, I attempted to access the KeePass application, importing the "passcode.kdbx" file. Despite employing the extracted passwords, my efforts proved futile. 

Determined to uncover a viable option, I decided to employ keyword searches, leading me to a noteworthy discovery. I found a reference to a desert concept, offering an alternative pattern:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/dc7aa714-f89f-4c50-be69-560ed50b2821)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e90c57cf-d0f0-4934-8404-642d2c0e626c)

This time, utilizing this new string as the password, I managed to successfully unlock the password file. With the successful import of the password database, I meticulously examined its contents, discovering a Putty private key & password within the notes section. The corresponding password was associated with the root user.

Attempting to employ the password for root access proved unsuccessful. However, it became evident that the Putty private key would be the key to my success. This private key was in ".ppk" format, requiring conversion to OpenSSH format for SSH login.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/601e0544-c1e8-4e58-9d7f-12e909cf03b5)

To facilitate this conversion, I installed Putty and utilized the Putty agent to transform the ".ppk" file into OpenSSH format:

```bash
sudo apt install putty

puttygen putty.ppk -O private-openssh -o id_rsa
```

With the successful conversion of the key file, I was able to employ it for SSH login as the root user, ultimately attaining root-level privileges. This accomplishment marked the culmination of my journey to obtain the root flag. (Pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/8d947ae9-a6b4-42f2-b6c1-b4b1d1f0ac44)

