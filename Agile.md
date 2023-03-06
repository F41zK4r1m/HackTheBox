  ![image](https://user-images.githubusercontent.com/87700008/223168750-f334d680-a040-485d-940b-838827fe3b62.png)

https://app.hackthebox.com/machines/Agile

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

Started with quick rustscan & found only 2 open ports : 22 & 80.

```
sudo rustscan -a 10.10.11.203 -- -sC -sV -T4 -vv -oN agile_nmap

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 f4:bc:ee:21:d7:1f:1a:a2:65:72:21:2d:5b:a6:f7:00 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCeVL2Hl8/LXWurlu46JyqOyvUHtAwTrz1EYdY5dXVi9BfpPwsPTf+zzflV+CGdflQRNFKPDS8RJuiXQa40xs9o=
|   256 65:c1:48:0d:88:cb:b9:75:a0:2c:a5:e6:37:7e:51:06 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEcaZPDjlx21ppN0y2dNT1Jb8aPZwfvugIeN6wdUH1cK
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://superpass.htb
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

From the ports scan results I found a new domain "superpass.htb" which is running on port 80.

I browsed through the webssite & observed that it's a website for the password manager.

![image](https://user-images.githubusercontent.com/87700008/223170926-fab167b8-95b5-4e64-a45a-a6ed05027fad.png)

I then ran directory scanning onto the domain & found 3 directories :

```
/download
/static
/vault
```

![image](https://user-images.githubusercontent.com/87700008/223171286-8d0ca373-7533-4988-be1a-5e0fd8949604.png)

I also checked for the VHOST running with the domain but didn't found anything.

![image](https://user-images.githubusercontent.com/87700008/223171871-6c16509d-d649-434f-8b9f-fef33dad8736.png)

