![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a8a7a42e-d73c-49ae-bd2f-632e2d32a9c2)

https://app.hackthebox.com/machines/555

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I started with the quick rustscan but it didn't revealed anything in the scan results:

```bash
sudo rustscan -a 10.10.10.226 -- -sC -sV -vv -oN download_nmap
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/1085cb2b-fb4d-435d-8798-983296cc6dfd)

Then I tried to browse the IP directly from the browser & observed that the page is unreachable but automatically resolving towards "download.htb".
Tried the same with curl & observed that traffic is getting redirected towards "http://download.htb":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/96f7c89f-796a-48ce-9bb8-2354d1f50d49)

I added the domain to the host config file & again started the rustscan towards the target using rustscan & this time I observed 2 open ports:

```bash
sudo rustscan -a download.htb -- -sC -sV -vv -oN download_nmap
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/deb74ea5-aeb0-4d7d-9b11-2ea20c435f03)

```Rust
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 cc:f1:63:46:e6:7a:0a:b8:ac:83:be:29:0f:d6:3f:09 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCuWCP0vWvijGHDhspMXZbTI7QnJUQp0r4tPbhbRxRgOJ+9BOh6GU7XOL3oL0ZCrcaT5UjnaoXlsWBNiP06QktmTwlyq4OnTjQfAqsgmv1EBoLGjMFkipeAFYbs5iN9heQ2YCrwaTxqksUY4WwrOKqnpGqHqfYRUf5hYOrRNDKuauVt+htRDt+DDkbcIHv8RNmZvffnhjKpzbYlJND/cHBMzADSKNO+01ZhQwqIj1Waq1DIzKHhAZXa8Dx7yQ1eV0Mfgy0FfXKz/79j2bCRSEIDAONCyIpVo/EYOwi7wY8DG3jZdId8MPrXvXDUiu4qMaRpSTHqSxchMrANoELluOKRwKX+jo4ieDLQ+8ds871tgE/4KVPDkAQesQSNB3KNlaUT40BdJvtcBDZrSSgqGIv9nUwfRfnFLtpnCIE7GI3eUi0AaLYsGzodmZM6xYk4iZJMnw9oNSrmVhBCiKcz/LM9IZytMlWA8jHVl51v19YjYn1csPEbZR3nXddcyN1A/qc=
|   256 2c:99:b4:b1:97:7a:8b:86:6d:37:c9:13:61:9f:bc:ff (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBDtcQEndNphvT9KpiBavbuC83D+p+RJ179gV8yI27QUxA9L/cy2s6B0GiEFpeyuvYQt+pRe5QpdYxwJzBkgrQj8=
|   256 e6:ff:77:94:12:40:7b:06:a2:97:7a:de:14:94:5b:ae (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAII/jAu2c8ySXaISmaxqbGzrYSe1+N5SwuHYYYe/yCrVJ
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-favicon: Unknown favicon MD5: A7E0469E13F02E350ABEB6DF724CE585
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Download.htb - Share Files With Ease
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

As port 80 is open & web server is running on it, I browsed through the domain "download.htb" & observed that it's a file sharing cloud service:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/191c71ae-05aa-4c39-b44c-4659fe22f25a)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Fuzzing:

To continue further enumeration I performed the VHOST & Sub-directory enumeration but didn't found anything.
