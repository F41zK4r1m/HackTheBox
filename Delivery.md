![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3730d7a9-8b21-49e5-a3e9-dc44c8a9d454)
https://app.hackthebox.com/machines/Delivery

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I initiated the enumeration process by conducting a port and service scan on the target system using the RustScan tool. This scan revealed the existence of three open ports: 22, 80, and 8065.

```Rust
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 9c:40:fa:85:9b:01:ac:ac:0e:bc:0c:19:51:8a:ee:27 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCq549E025Q9FR27LDR6WZRQ52ikKjKUQLmE9ndEKjB0i1qOoL+WzkvqTdqEU6fFW6AqUIdSEd7GMNSMOk66otFgSoerK6MmH5IZjy4JqMoNVPDdWfmEiagBlG3H7IZ7yAO8gcg0RRrIQjE7XTMV09GmxEUtjojoLoqudUvbUi8COHCO6baVmyjZRlXRCQ6qTKIxRZbUAo0GOY8bYmf9sMLf70w6u/xbE2EYDFH+w60ES2K906x7lyfEPe73NfAIEhHNL8DBAUfQWzQjVjYNOLqGp/WdlKA1RLAOklpIdJQ9iehsH0q6nqjeTUv47mIHUiqaM+vlkCEAN3AAQH5mB/1
|   256 5a:0c:c0:3b:9b:76:55:2e:6e:c4:f4:b9:5d:76:17:09 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAiAKnk2lw0GxzzqMXNsPQ1bTk35WwxCa3ED5H34T1yYMiXnRlfssJwso60D34/IM8vYXH0rznR9tHvjdN7R3hY=
|   256 b7:9d:f7:48:9d:a2:f2:76:30:fd:42:d3:35:3a:80:8c (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEV5D6eYjySqfhW4l4IF1SZkZHxIRihnY6Mn6D8mLEW7
80/tcp   open  http    syn-ack ttl 63 nginx 1.14.2
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-server-header: nginx/1.14.2
|_http-title: Welcome
8065/tcp open  unknown syn-ack ttl 63
| fingerprint-strings: 
|   GenericLines, Help, RTSPRequest, SSLSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Accept-Ranges: bytes
|     Cache-Control: no-cache, max-age=31556926, public
|     Content-Length: 3108
|     Content-Security-Policy: frame-ancestors 'self'; script-src 'self' cdn.rudderlabs.com
|     Content-Type: text/html; charset=utf-8
|     Last-Modified: Tue, 08 Aug 2023 06:18:13 GMT
|     X-Frame-Options: SAMEORIGIN
|     X-Request-Id: w3f7dd9tw781tfh4j46ppfmdzh
|     X-Version-Id: 5.30.0.5.30.1.57fb31b889bf81d99d8af8176d4bbaaa.false
|     Date: Tue, 08 Aug 2023 19:24:42 GMT
|     <!doctype html><html lang="en"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=0"><meta name="robots" content="noindex, nofollow"><meta name="referrer" content="no-referrer"><title>Mattermost</title><meta name="mobile-web-app-capable" content="yes"><meta name="application-name" content="Mattermost"><meta name="format-detection" content="telephone=no"><link re
|   HTTPOptions: 
|     HTTP/1.0 405 Method Not Allowed
|     Date: Tue, 08 Aug 2023 19:24:43 GMT
|_    Content-Length: 0
```

Port 22 was identified as running SSH, while ports 80 and 8065 were found to be associated with HTTP servers.

Upon navigating to port 80 via a web browser, I encountered a webpage that appeared to be related to e-mail support:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/bb840326-732e-4ce8-a689-c65376ba78ab)


Digging into the HTML source code, I discovered references to two domains: "delivery.htb" and "helpdesk.delivery.htb". To ensure seamless access, I added these domains to my hosts file:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3f6887e4-a83f-4de7-8faa-1a7eebbefb4a)

Subsequently, I explored the "helpdesk.delivery.htb" domain, which was hosting an "OSQuery" application. This platform allowed users to raise and track ticket statuses:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/fc6196e6-7139-4788-982e-061d82a90b74)

Concurrently, I investigated the HTTP host running on port 8065, where I encountered a service titled "**Mattermost**":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/811f7b2b-5cea-43a9-b24b-abe0cffe7b78)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Fuzzing:

With a better understanding of the services operating on distinct ports and domains, I commenced the sub-directory enumeration process using GoBuster. This meticulous effort yielded a multitude of results:

For the "helpdesk.delivery.htb" domain:

```bash
gobuster dir -u http://helpdesk.delivery.htb/ -t 20 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,php -e -b 404,403 -k
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4a9e5208-2b3d-42b1-9cb8-9536f0deddcb)

For the "delivery.htb:8065" domain:

```bash
gobuster dir -u http://delivery.htb:8065/ -t 20 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,php -e -b 404,403 -k --exclude-length 3108
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/08743205-8878-49bb-9b6b-ef4d8bc079f2)

During my exploration of "http://helpdesk.delivery.htb/index.php", I discovered an "open ticket" option:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/1aa41cd1-6fae-44b6-abc9-43c8ad84e40d)

I initiated the process of raising a ticket by providing basic information such as a username and email address. Upon submission, I was assigned a ticket number, which I could use to check the ticket status:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/da0aa1e4-3f18-43f9-97a0-3e697ad0aa26)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0c2cd384-61d0-43ad-94a0-79b0f7cc6eb7)

Shifting my focus to the Mattermost domain, I attempted to register using a fabricated email address. However, this process required a confirmation email, and since the machine lacked internet connectivity, I had to provide an email address within the "delivery.htb" network to receive the verification link.

It was during the ticket creation process that I learned about the ability to update a ticket by simply emailing a query to "**ticket_no.@delivery.htb**". Armed with access to this ticket email, I seized the opportunity to register an account on the "MatterMost" domain:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3e4d5947-7f4d-49d7-90f1-b776d67f8401)

Upon completing the registration details, I checked the ticket queue and discovered the verification email waiting for me:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/059a283f-9f00-4a8d-b525-d69930e40330)

Following the link within the email successfully verified my account, granting me access to the platform:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c038998b-9058-434f-82a5-1eee770bbde7)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

Upon successfully logging into the MatterMost domain, I discerned its resemblance to communication platforms like Slack, characterized by various channels. Among these channels, an internal one captured my attention as it contained the credentials for the "mailserver":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ccd1368e-e017-4cf1-b94c-42c271a3deb4)

Although these credentials were designated for the "mailserver," I decided to explore their potential for SSH access. To my delight, the SSH connection attempt was successful, marking my initial entry into the target system and granting me the coveted user flag: (pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9528b60b-a342-400b-b726-bb2b0659da29)


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Root.txt:

Having secured the user flag, my next endeavor was to achieve full control over the system, thereby obtaining the elusive root flag. My exploration led me to a thorough inspection of sudo permissions, which unfortunately yielded no promising results:

```bash
sudo -l
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9baed0da-8b81-471d-8daa-d0520fc97e0e)

Similarly, investigating the cron jobs didn't offer any breakthrough:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/cd68f7c2-0ff1-4b40-8ed2-baf0a8577143)

However, as I combed through the **/var/opt/mattermost** directory, I stumbled upon the **config.json** file. It revealed MySQL credentials:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d9cd7af2-7e94-4586-ae4e-652d91a39d77)

Utilizing these credentials, I accessed the MySQL database. There, I discovered a table named **users** containing user IDs, usernames, and password hashes:

```mysql
mysql -u mmuser -p #enter the SQL credentials

show databases;
use mattermost;
descride users;
select ID, Username, Password from Users;
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/09550379-d21d-4866-962a-c19a0f9d7ea0)

Having retrieved the root user hash, I embarked on cracking it. Despite my initial unsuccessful attempts using the common "rockyou" wordlist, a clue from an internal channel message caught my attention. The hint was that "PleaseSubscribe!" might not be present in "rockyou," but a more advanced hash-cracking tool like Hashcat could be employed.

To resolve this, I generated a new wordlist using the "best64" rule from Hashcat:

```bash
hashcat --stdout password -r /usr/share/hashcat/rules/best64.rule > new_pass_list #where password file contains "PleaseSubscribe!"
```

Subsequently, utilizing haiti to identify the password hash algorithm and then employing John the Ripper with the newly created password list, I was astounded to find that the hash was cracked in mere seconds:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/cea17abc-8fa9-4198-b8d3-de2b12d97382)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e4af615f-fbdc-4e37-be2e-815c1779d2b3)

Equipped with the cracked credentials, I triumphantly logged in as the root user and triumphantly claimed the root flag. (pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f91e71a9-f07a-4d92-a945-da290742e6bd)

