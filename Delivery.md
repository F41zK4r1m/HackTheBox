![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3730d7a9-8b21-49e5-a3e9-dc44c8a9d454)
https://app.hackthebox.com/machines/Delivery

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I started with port & service scan running on the target using rustscan & found 3 open ports: 22, 80, 8065

```
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

While port 22 is running SSH, 80 & 8065 running HTTP servers.

I browsed through the port 80 on the browser & observed the webpage is about the e-mail related support:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/bb840326-732e-4ce8-a689-c65376ba78ab)


In the HTML page source I observed 2 domains "delivery.htb" & "helpdesk.delivery.htb", which I added to my hosts file:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3f6887e4-a83f-4de7-8faa-1a7eebbefb4a)

The helpdesk domain is running "OSQuery" application to raise & check ticket status:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/fc6196e6-7139-4788-982e-061d82a90b74)

As checked the HTTP host on port 8065, I found that there is a service running called "**Mattermost**":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/811f7b2b-5cea-43a9-b24b-abe0cffe7b78)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Fuzzing:

Once I got the idea of the services running on different ports & domains, I initiated sub-directory enumeration using GoBuster & got multiple results:

```bash
gobuster dir -u http://helpdesk.delivery.htb/ -t 20 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,php -e -b 404,403 -k
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4a9e5208-2b3d-42b1-9cb8-9536f0deddcb)

```bash
gobuster dir -u http://delivery.htb:8065/ -t 20 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,php -e -b 404,403 -k --exclude-length 3108
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/08743205-8878-49bb-9b6b-ef4d8bc079f2)

While browsing through "http://helpdesk.delivery.htb/index.php", there is an "open ticket" option:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/1aa41cd1-6fae-44b6-abc9-43c8ad84e40d)

I raised a ticket by filling the basic details of usernaeme & email. After filling the details I got the option to check the ticket status with the ticket no:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/da0aa1e4-3f18-43f9-97a0-3e697ad0aa26)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0c2cd384-61d0-43ad-94a0-79b0f7cc6eb7)

Next, I went onto the Mattermost domain & tried registering with the fake e-mail but it sends the confirmation e-mail link to the email id & since the box isn't having the internet connection I have to provide the e-mail where I will get verification link which is available inside the "delivery.htb" network.

So, while raising the ticket I got the info that if I want to add more data/query to the ticket I can just e-mail the query to "ticket_no.@delivery.htb" & it will be auto updated in the ticket. By this ticket mail id for which I am having the access as well, I can register my self in the "MatterMost" domain.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3e4d5947-7f4d-49d7-90f1-b776d67f8401)

Once filling the details for account creation I check the ticket queue & I got the verficitation e-mail there:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/059a283f-9f00-4a8d-b525-d69930e40330)

Going through the link verified my account & I got the access:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c038998b-9058-434f-82a5-1eee770bbde7)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

Once I logged into the MatterMost domain, I observed that it some kind of chat application juts like Slack & there were few different kind of channels here in which one of the internal channel contains the "mailserver" credentials:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ccd1368e-e017-4cf1-b94c-42c271a3deb4)

Although the credentials belongs to "mailserver" but I though to give it a try with SSH. Using it with SSH finally gave me the initial access on the target host along with the user flag: (pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9528b60b-a342-400b-b726-bb2b0659da29)




