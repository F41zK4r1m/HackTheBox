![image](https://github.com/user-attachments/assets/a35c4df1-f61b-4ef2-ad02-686226ecad5c)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration:

I began the enumeration process with a quick `rustscan`, which revealed two open ports on the target host:

```
rustscan -a 10.10.11.11 -- -A -T4 -vv -oN broad_nmap
```

```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 06:2d:3b:85:10:59:ff:73:66:27:7f:0e:ae:03:ea:f4 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDH0dV4gtJNo8ixEEBDxhUId6Pc/8iNLX16+zpUCIgmxxl5TivDMLg2JvXorp4F2r8ci44CESUlnMHRSYNtlLttiIZHpTML7ktFHbNexvOAJqE1lIlQlGjWBU1hWq6Y6n1tuUANOd5U+Yc0/h53gKu5nXTQTy1c9CLbQfaYvFjnzrR3NQ6Hw7ih5u3mEjJngP+Sq+dpzUcnFe1BekvBPrxdAJwN6w+MSpGFyQSAkUthrOE4JRnpa6jSsTjXODDjioNkp2NLkKa73Yc2DHk3evNUXfa+P8oWFBk8ZXSHFyeOoNkcqkPCrkevB71NdFtn3Fd/Ar07co0ygw90Vb2q34cu1Jo/1oPV1UFsvcwaKJuxBKozH+VA0F9hyriPKjsvTRCbkFjweLxCib5phagHu6K5KEYC+VmWbCUnWyvYZauJ1/t5xQqqi9UWssRjbE1mI0Krq2Zb97qnONhzcclAPVpvEVdCCcl0rYZjQt6VI1PzHha56JepZCFCNvX3FVxYzEk=
|   256 59:03:dc:52:87:3a:35:99:34:44:74:33:78:31:35:fb (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBK7G5PgPkbp1awVqM5uOpMJ/xVrNirmwIT21bMG/+jihUY8rOXxSbidRfC9KgvSDC4flMsPZUrWziSuBDJAra5g=
|   256 ab:13:38:e4:3e:e0:24:b4:69:38:a9:63:82:38:dd:f4 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILHj/lr3X40pR3k9+uYJk4oSjdULCK0DlOxbiL66ZRWg
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Linux 5.0 (97%), Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=8/13%OT=22%CT=%CU=32392%PV=Y%DS=2%DC=T%G=N%TM=66BAF221%P=x86_64-pc-linux-gnu)
SEQ(SP=106%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=A)
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

Uptime guess: 49.377 days (since Mon Jun 24 16:38:28 2024)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=262 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 443/tcp)
HOP RTT       ADDRESS
1   150.49 ms 10.10.14.1
2   151.16 ms 10.10.11.11
```

## Web enumeration:

Moving further, when I browsed the website, I observed that it provides cybersecurity-related services:

![image](https://github.com/user-attachments/assets/af23c0de-7f8d-48a4-b03a-9d089bdb8784)

At the bottom of the website, I noticed a domain, `board.htb`, which I then added to my hosts configuration file:

![image](https://github.com/user-attachments/assets/b8ac2e4a-2163-43e0-bb0f-06f6d33ca0a4)

After adding the domain to the hosts file, I performed a directory search and subdomain enumeration. I didn't find anything from the directory scan:

```
dirsearch -u http://board.htb -x 404,403 --crawl
```

![image](https://github.com/user-attachments/assets/4ca6f63a-f984-4c1f-85be-df676cc8fc82)

However, I discovered another domain from the subdomain scan and added `crm.board.htb` to my hosts file:

```
ffuf -H "Host: FUZZ.board.htb" -u http://10.10.11.11 -w /usr/share/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 15949
```

![image](https://github.com/user-attachments/assets/452c0c21-2df4-4944-8b06-877a722943d4)

When I browsed this domain, I observed the `Dolibarr CRM` login page:

![image](https://github.com/user-attachments/assets/2249b79b-6a78-4c9b-87cb-4599ebe76d4a)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Initial access:

I ran the directory search again, and this time I got numerous results:

![image](https://github.com/user-attachments/assets/81487c21-ba4f-49b0-9889-c6f08387529e)

When I tried to access the `Dolibarr CRM` using the default `admin:admin` credentials, I successfully logged in:

![image](https://github.com/user-attachments/assets/ad8c7ec1-69c9-463b-976d-fb49d2e8e01b)

Since the application was running on version `17.0.0`, I started searching for an exploit and found that this version is vulnerable to code injection, specifically identified by CVE: `CVE-2023-30253`. I also found plenty of proof-of-concepts (POCs) on GitHub and utilized this [exploit](https://github.com/nikn0laty/Exploit-for-Dolibarr-17.0.0-CVE-2023-30253) to achieve Remote Code Execution (RCE):

![image](https://github.com/user-attachments/assets/3c3a6693-2199-46bb-a7a9-f65d7ad1b075)

After cloning the repository, I ran the exploit while keeping Netcat running, and I quickly gained a shell as the `www-data` user. 🙂

![image](https://github.com/user-attachments/assets/9eecae05-673d-4f32-b425-374018efd7bb)

## User flag:

Although I gained access, I still didn't have access to the user flag, as it was likely located in the `larissa` home folder, to which I didn’t have access.

I began searching for ways to escalate my privileges or gain access to the `larissa` account. During my search, I noticed a configuration file in the `/var/www/html/crm.board.htb/htdocs` folder, which usually contains important configuration information. In the `conf` directory, I found a `conf.php` file, and upon analyzing its script, I discovered some SQL-related credentials:

![image](https://github.com/user-attachments/assets/cc5bfb9d-4c48-4296-824e-fc46433a6961)

I used these credentials to access the MySQL database:

![image](https://github.com/user-attachments/assets/e36f47d4-6cf4-49e2-aa58-6c9040c4a1f8)

While examining the `dolibarr` database, I found a table named `llx_user`. When I queried this table, I retrieved a large amount of data:

![image](https://github.com/user-attachments/assets/34f0f807-ddbe-427e-9082-42dab3a50887)

To focus on the essential information, I checked the most important fields and found hashed credentials for two users:

![image](https://github.com/user-attachments/assets/e12c8475-08ed-410c-b1d5-9174175e3428)

I attempted to crack the hash using `John the Ripper` but was unsuccessful. Instead, I tried using the same password for SSH login with the `larissa` user, and it worked! 🙂

![image](https://github.com/user-attachments/assets/b91988bc-9dca-46fd-bbb4-51e854b84b5d)

Finally, I found the user flag in the `larissa` home directory.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Privilege Escalation:

After fetching the user flag, I began my search for the root flag, which required escalating my privileges. I started by checking the sudo permissions for the `larissa` user, but found that no privileges were assigned.

Next, I checked for SUID binaries and found an uncommon binary in the list:

```
find / -perm -u=s -type f 2>/dev/null

...
...
/usr/lib/x86_64-linux-gnu/enlightenment/modules/cpufreq/linux-gnu-x86_64-0.23.1/freqset
..
..
```

![image](https://github.com/user-attachments/assets/739f58b5-9341-4d5a-b03d-c7ad52f3735b)

When I searched for information on `enlightenment`, I discovered that this process helps manage graphics memory across UNIX environments. I then searched for an `enlightenment exploit`, and the first result was a [GitHub POC](https://github.com/MaherAzzouzi/CVE-2022-37706-LPE-exploit) related to a Local Privilege Escalation (LPE) exploit, assigned CVE `CVE-2022-37706`.

I transferred the exploit to the target host and executed it. Shortly after execution, I successfully obtained a shell as the root user. 🙂

![image](https://github.com/user-attachments/assets/25641eda-b27e-454f-af05-07e441681077)

With root access, I was able to retrieve the final root flag. (pwn3d!🎉)
