![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/addb842d-c4bf-4aed-b681-0ffc74ab64f0)

https://app.hackthebox.com/machines/Help

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I began with the quick rustscan & observed 3 open port from the scan, i.e. 22, 80, 3000.

```bash
sudo rustscan -a 10.10.10.121 -- -sC -sV -vv -oN help_nmap
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/63f363fd-7d91-4d9d-bbc9-8fed5c1e10a1)

```Rust
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e5:bb:4d:9c:de:af:6b:bf:ba:8c:22:7a:d8:d7:43:28 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCZY4jlvWqpdi8bJPUnSkjWmz92KRwr2G6xCttorHM8Rq2eCEAe1ALqpgU44L3potYUZvaJuEIsBVUSPlsKv+ds8nS7Mva9e9ztlad/fzBlyBpkiYxty+peoIzn4lUNSadPLtYH6khzN2PwEJYtM/b6BLlAAY5mDsSF0Cz3wsPbnu87fNdd7WO0PKsqRtHpokjkJ22uYJoDSAM06D7uBuegMK/sWTVtrsDakb1Tb6H8+D0y6ZQoE7XyHSqD0OABV3ON39GzLBOnob4Gq8aegKBMa3hT/Xx9Iac6t5neiIABnG4UP03gm207oGIFHvlElGUR809Q9qCJ0nZsup4bNqa/
|   256 d5:b0:10:50:74:86:a3:9f:c5:53:6f:3b:4a:24:61:19 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBHINVMyTivG0LmhaVZxiIESQuWxvN2jt87kYiuPY2jyaPBD4DEt8e/1kN/4GMWj1b3FE7e8nxCL4PF/lR9XjEis=
|   256 e2:1b:88:d3:76:21:d4:1e:38:15:4a:81:11:b7:99:07 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHxDPln3rCQj04xFAKyecXJaANrW3MBZJmbhtL4SuDYX
80/tcp   open  http    syn-ack ttl 63 Apache httpd 2.4.18
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Did not follow redirect to http://help.htb/
3000/tcp open  http    syn-ack ttl 63 Node.js Express framework
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Site doesn't have a title (application/json; charset=utf-8).
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

From the scan results I observed that:

- On port 22 SSH is running, which is default.
- On port 80 HTTP server is running with the domain: "help.htb", which I added to my host config file.
- Port 3000 also hosted a HTTP server with "Node.js Express framework".

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Fuzzing:

After fininshing the port scan I browsed through the domain "http://help.htb" & found it's just an default Apache server page.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f0c31e9e-9df0-4219-91de-36cb918a90d9)

Moved onto the port 3000 & observed just a note: "Hi Shiv, To get access please find the credentials with given query"

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/828494b0-b3e3-4fa2-946c-791597e9d63f)
