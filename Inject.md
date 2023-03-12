  ![image](https://user-images.githubusercontent.com/87700008/224529412-369cf59f-2320-4a8b-981e-a100f210e4c2.png)
https://app.hackthebox.com/machines/533

## Enumeration :

Started with quick rustscan & found 2 open ports: 22 & 8080

```
sudo rustscan -a 10.10.11.204 -- -sC -sV -T4 -vv -oN inject_nmap
```

![image](https://user-images.githubusercontent.com/87700008/224529454-b7d64af4-88a8-403d-a978-21a25aa68fc4.png)

```
PORT     STATE SERVICE     REASON         VERSION
22/tcp   open  ssh         syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 ca:f1:0c:51:5a:59:62:77:f0:a8:0c:5c:7c:8d:da:f8 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDKZNtFBY2xMX8oDH/EtIMngGHpVX5fyuJLp9ig7NIC9XooaPtK60FoxOLcRr4iccW/9L2GWpp6kT777UzcKtYoijOCtctNClc6tG1hvohEAyXeNunG7GN+Lftc8eb4C6DooZY7oSeO++PgK5oRi3/tg+FSFSi6UZCsjci1NRj/0ywqzl/ytMzq5YoGfzRzIN3HYdFF8RHoW8qs8vcPsEMsbdsy1aGRbslKA2l1qmejyU9cukyGkFjYZsyVj1hEPn9V/uVafdgzNOvopQlg/yozTzN+LZ2rJO7/CCK3cjchnnPZZfeck85k5sw1G5uVGq38qcusfIfCnZlsn2FZzP2BXo5VEoO2IIRudCgJWTzb8urJ6JAWc1h0r6cUlxGdOvSSQQO6Yz1MhN9omUD9r4A5ag4cbI09c1KOnjzIM8hAWlwUDOKlaohgPtSbnZoGuyyHV/oyZu+/1w4HJWJy6urA43u1PFTonOyMkzJZihWNnkHhqrjeVsHTywFPUmTODb8=
|   256 d5:1c:81:c9:7b:07:6b:1c:c1:b4:29:25:4b:52:21:9f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIUJSpBOORoHb6HHQkePUztvh85c2F5k5zMDp+hjFhD8VRC2uKJni1FLYkxVPc/yY3Km7Sg1GzTyoGUxvy+EIsg=
|   256 db:1d:8c:eb:94:72:b0:d3:ed:44:b9:6c:93:a7:f9:1d (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICZzUvDL0INOklR7AH+iFw+uX+nkJtcw7V+1AsMO9P7p
8080/tcp open  nagios-nsca syn-ack ttl 63 Nagios NSCA
| http-methods: 
|_  Supported Methods: GET HEAD OPTIONS
|_http-title: Home
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

When checked the website running on port 8080 found it's providing the cloud & file share services :

![image](https://user-images.githubusercontent.com/87700008/224534505-28220236-e135-4893-9981-26d5478f513e.png)

By going onto the '/blogs' section I found 3 blogs written by 2 users : "admin" & " Brandon Auger"

![image](https://user-images.githubusercontent.com/87700008/224534657-01d2e78c-10d3-4e3c-987b-888afa023021.png)

Moving further when I checked the HTML source code of the page I didn't found anything special. So, I moved onto the directory list & subdomain enumeration.

In subdomain or VHOST enumeration & I didn't found anything :

![image](https://user-images.githubusercontent.com/87700008/224540315-9024f38b-f8a5-4641-8cad-c6d61dccf4da.png)

In directory list I found some directories :

![image](https://user-images.githubusercontent.com/87700008/224540364-b533d963-7d2f-4667-a516-20b71d806188.png)

I started checking with 'release_notes' but on this page there are only release notifications :

![image](https://user-images.githubusercontent.com/87700008/224542030-b05e0ee7-d985-42e5-aad6-c3ae5723ffb2.png)

Then I tried to register on the page but the page is under the construction :

![image](https://user-images.githubusercontent.com/87700008/224542194-41843693-c291-404f-8c54-4603d81f1417.png)

Then I moved onto the '/uploads' section & after uploading the php reverse shell I observed that I can only upload image files. So, I uploaded on the file with '.jpg' extension & found that it's leading me to another page where I can see my uploaded image.

      /show_image?img=php-reverse-shell.php.jpg
      
 ![image](https://user-images.githubusercontent.com/87700008/224542405-281c94f6-e0d0-4a33-91df-510489a706b9.png)

I tried the '/show_image?img=' path with some LFI payloads & started checking with '/etc/passwd'. After performing multiple payloads I entered the below payload & observed 200 response.

    &.//&.//&.//&.//&.//etc/group
    
![image](https://user-images.githubusercontent.com/87700008/224542604-ab7a9e08-3a79-4bba-82f9-3e363a24ec51.png)

I am confirmed here that this path is vulnerable to LFI but I still not able to read the /etc/passwd content, so again I tried some LFI payloads & after some times I got success with this payload :

    ../../../../../../../../../../../../../../../../etc/passwd
    
![image](https://user-images.githubusercontent.com/87700008/224542688-f6d088b2-33e1-49dd-b149-684d6b969433.png)

I found 2 users in the above list 'frank' & 'phil'
