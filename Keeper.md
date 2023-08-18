![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/566af2b4-6348-4358-b8cc-0aa972723d92)

https://app.hackthebox.com/machines/Keeper

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I started my enumeration with the quick rustscan to check for open ports & services running. After the scan is complete I only observed 2 open ports:

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

Once the scan is complete & I found that port 80 is open & http server is running, I browsed the IP & observed a domain on the webpage.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/fd45cca8-24c2-4e23-9e8c-06e1ce6fc61f)

I added these domains "keeper.htb" & "tickets.keeper.htb" to my host config file.
