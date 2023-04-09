 ![image](https://user-images.githubusercontent.com/87700008/230741085-ae68684e-fb9c-4a26-a17f-75349dc030eb.png)

https://app.hackthebox.com/machines/537

## Enumeration:

Started with the port scanning & found 2 open 22 & 80.

```
sudo rustscan -a 10.10.11.208 -- -sC -sV -vv -oN busqueda_nmap
```

```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 4f:e3:a6:67:a2:27:f9:11:8d:c3:0e:d7:73:a0:2c:28 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIzAFurw3qLK4OEzrjFarOhWslRrQ3K/MDVL2opfXQLI+zYXSwqofxsf8v2MEZuIGj6540YrzldnPf8CTFSW2rk=
|   256 81:6e:78:76:6b:8a:ea:7d:1b:ab:d4:36:b7:f8:ec:c4 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPTtbUicaITwpKjAQWp8Dkq1glFodwroxhLwJo6hRBUK
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.52
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Did not follow redirect to http://searcher.htb/
Service Info: Host: searcher.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

In the scan results I observed a domain 'searcher.htb' running on port 80. I added the domain to the /etc/hosts file.

I then perform vhost & sub-directory enumeration on the domain but found nothing.

I then browsed through the website & found that it's a kind of search tool which which provides search results from the different search engines available accross the internet.

![image](https://user-images.githubusercontent.com/87700008/230746368-c6b50271-af99-4b23-9e81-10eed4016f30.png)

