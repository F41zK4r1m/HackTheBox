   ![image](https://user-images.githubusercontent.com/87700008/209568214-13059ca8-21b0-4432-878a-613bce7c9ae0.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Enumeration:**

I started with quick rustscan, which gave me 3 open ports :

    sudo rustscan -a 10.10.11.194 -- -sC -sV -T4 -vv -oN soccer_nmap
    
    Open 10.10.11.194:22 --> OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
    Open 10.10.11.194:80  --> nginx 1.18.0
    Open 10.10.11.194:9091  --> xmltec-xmlmail?
    
![image](https://user-images.githubusercontent.com/87700008/209568575-b6901fdf-1711-46a6-adf4-f51cc81e8c60.png)

Found a domain to resolve in the port scan results & added it to the hosts list, i.e. "http://soccer.htb"

![image](https://user-images.githubusercontent.com/87700008/209568773-bbec4b87-83cc-45b7-85fc-5f461bcd57fa.png)

Went to the website & found a regular page with very limited options :

![image](https://user-images.githubusercontent.com/87700008/209573968-b71a2436-351b-49ae-be3e-2869e368745e.png)
![image](https://user-images.githubusercontent.com/87700008/209573987-a8d60bf5-31f7-4db4-8dae-3b859d6a6e6e.png)

Next, I started the I scanned the website using gobuster for the subdomains & found only 1 subdomain in the result :

      gobuster dir -u http://soccer.htb -t 20 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o soccer -b 404,403 -k
      
      /tiny                 (Status: 301) [Size: 178] [--> http://soccer.htb/tiny/]

![image](https://user-images.githubusercontent.com/87700008/209574154-23813b95-770e-4898-a2d3-9b0dec061e11.png)

I quickly checked the website & found a login panel in it, URL redirected me to another URL, i.e. "http://soccer.htb/tiny/tinyfilemanager.php"

![image](https://user-images.githubusercontent.com/87700008/209574206-1a91a307-3bfd-4bfd-bd6c-fd26dbee2107.png)

This looks like a some kind of file manager. I scanned this subdomain with the gobuster & found another subdomain :

      /uploads              (Status: 301) [Size: 178] [--> http://soccer.htb/tiny/uploads/]
      
![image](https://user-images.githubusercontent.com/87700008/209574374-1687fd45-d4b2-452b-bf7e-80fdb19e3368.png)

I tried to access it but I need to login before accessing it as it showed 403 error 😕

![image](https://user-images.githubusercontent.com/87700008/209574724-fb3c57a6-54c8-47bf-97ff-5fb5670bed37.png)

I then moved back to the '/tiny' login page & checked for the source code & found that it's running on version 2.4.3

![image](https://user-images.githubusercontent.com/87700008/209574936-eb3044f6-ea5c-4656-bcaa-60baefeda7ea.png)

I checked for the exploits of 'tiny file manager 2.4' on google & found many refrences. Such as :

      Exploit DB : https://www.exploit-db.com/exploits/50828 (Tiny File Manager RCE <= 2.4.6)
      GitHub : https://github.com/febinrev/tinyfilemanager-2.4.3-exploit (Path traversal)
      
But both the exploit needs an authenticated user for the successfull exploit. So, I tried the different kind of SQL injection to check whether it's vulnerable to SQLi but no luck. 😕

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Initial access :**

Then I tried the default credentials for the file manager, i.e. 'admin' : 'admin@123' & it's worked.🙂

When logged in found a file/website manager kind of page.

![image](https://user-images.githubusercontent.com/87700008/209575440-187c7121-2ed4-4536-8e81-f1ed1275ceab.png)

And now I can access the upload section as well.

![image](https://user-images.githubusercontent.com/87700008/209575549-dd34c181-f63d-445a-8918-b5c0d22b6546.png)

I stucked at this point as the exploit POC is not working as expected & it seems like I can't upload the data to the regular path.

![image](https://user-images.githubusercontent.com/87700008/209581350-83e8e96b-d088-43bb-9356-818a5d2e7dc1.png)
![image](https://user-images.githubusercontent.com/87700008/209581447-e62efb7e-1920-40cd-8637-3b13414d1ee2.png)

I tried other possible way to exploit path traversal vulnerability & LFI but no luck in all of them. I have to figure out the correct path to upload the file.
After searching for approx 2 hours I landed onto this page, where I observed that I can upload/write my files : http://soccer.htb/tiny/tinyfilemanager.php?p=tiny%2Fuploads&upload

I uploaded a PHP reverse shell & started listening on my kali machine, browsed on my uploaded file & catched the reverse shell.(pwn3d!🙂)

![image](https://user-images.githubusercontent.com/87700008/209581628-4ddafa8b-d05c-4b82-ab38-3032e67e5d27.png)

The shell is not stable so first thing I did is stablized the shell, using these coomands :

      python3 -c 'import pty;pty.spawn("/bin/bash")'
      export TERM=xterm
      stty raw -echo; fg
      
So, currently I am running as 'www-data' & there is another user directory present, i.e. 'player'. Player directory is having the user flag but I don't have access to it.

![image](https://user-images.githubusercontent.com/87700008/209581967-922a793c-9e44-45db-ac79-5f4dac792b8f.png)



