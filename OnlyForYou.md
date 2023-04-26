![image](https://user-images.githubusercontent.com/87700008/234083046-ab3f1253-4c94-48aa-9e91-c7f5bb9d582c.png)

 https://app.hackthebox.com/machines/OnlyForYou
 
 ## Enumeration:
 
 Started with the quick rustscan & observed 2 open ports 80 & 22.
 
 ```
 sudo rustscan -a 10.10.11.210 -- -sC -sV -vv -oN onlyforyou_nmap
 
 PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e8:83:e0:a9:fd:43:df:38:19:8a:aa:35:43:84:11:ec (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDX7r34pmJ6U9KrHg0/WDdrofcOXqTr13Iix+3D5ChuYwY2fmqIBlfuDo0Cz0xLnb/jaT3ODuDtmAih6unQluWw3RAf03l/tHxXfvXlWBE3I7uDu+roHQM7+hyShn+559JweJlofiYKHjaErMp33DI22BjviMrCGabALgWALCwjqaV7Dt6ogSllj+09trFFwr2xzzrqhQVMdUdljle99R41Hzle7QTl4maonlUAdd2Ok41ACIu/N2G/iE61snOmAzYXGE8X6/7eqynhkC4AaWgV8h0CwLeCCMj4giBgOo6EvyJCBgoMp/wH/90U477WiJQZrjO9vgrh2/cjLDDowpKJDrDIcDWdh0aE42JVAWuu7IDrv0oKBLGlyznE1eZsX2u1FH8EGYXkl58GrmFbyIT83HsXjF1+rapAUtG0Zi9JskF/DPy5+1HDWJShfwhLsfqMuuyEdotL4Vzw8ZWCIQ4TVXMUwFfVkvf410tIFYEUaVk5f9pVVfYvQsCULQb+/uc=
|   256 83:f2:35:22:9b:03:86:0c:16:cf:b3:fa:9f:5a:cd:08 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAz/tMC3s/5jKIZRgBD078k7/6DY8NBXEE8ytGQd9DjIIvZdSpwyOzeLABxydMR79kDrMyX+vTP0VY5132jMo5w=
|   256 44:5f:7a:a3:77:69:0a:77:78:9b:04:e0:9f:11:db:80 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOqatISwZi/EOVbwqfFbhx22EEv6f+8YgmQFknTvg0wr
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://only4you.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

After the port scanning I observed a domain running on port 80, i.e. : http://only4you.htb/. I added the domain to the hosts file.

I checked website & html source code but didn't found anything useful to get initial access.

After that I scanned for the sub-directories & VHOST enumeration. In the sub-directory enumeration I didn't found anything but in the VHOST enumeration I observed a domain 'beta.only4you.htb'.

![image](https://user-images.githubusercontent.com/87700008/234087689-bf77eafe-7c74-4c13-a16d-ba5daaf82151.png)

The beta website is providing some functionality to resize & convert the images, in the home page of the website I found the source code written in python to perform reszie & conversion.

![image](https://user-images.githubusercontent.com/87700008/234435248-c2ce1fa5-0fff-4caa-bf36-5f53400d94b4.png)

I again ran the sub-directory enumeration for the beta website & found 5 sub-directories:
![image](https://user-images.githubusercontent.com/87700008/234435295-e8f7400b-4f58-4c92-b8bc-ebcb36958804.png)

At this point, I browsed through the beta website & checked all the fucntions manually, since there is an upload option I thought there might be some vulnerability exist in the upload fucntion.

I started checking the source code & in the "**app.py**" I found this part of the '/download' function vulnerable to LFI, the code checks if the image filename contains the string '..' or starts with '../'. However, it only performs a basic check and doesn't sanitize the user input.

![image](https://user-images.githubusercontent.com/87700008/234435937-271b662e-83ee-4cc2-aae6-f991943217f6.png)

To confirm this, I fired up my burp suite & intercepted the download request after uploading a random image file. 
After few trial & test I finally confirmed the LFI vulnerability in the Image parameter:

![image](https://user-images.githubusercontent.com/87700008/234436376-98147449-e97a-4714-a22f-d08c40e43a63.png)

In the **passwd** file I observed 3 users apart from the root:
```
john
neo4j
dev
```

