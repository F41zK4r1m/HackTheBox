https://app.hackthebox.com/machines/MetaTwo

![image](https://user-images.githubusercontent.com/87700008/204091344-de25ad85-8c3c-4687-b735-c111131c05f4.png)

**Enumeration**

Port scan :

Starting with a quick Rustscan :

    sudo rustscan -a 10.10.11.186 -- -A -T4 -vv

Found 3 open ports : 

    - 21 FTP
    - 22 SSH
    - 80 HTTP
    
![image](https://user-images.githubusercontent.com/87700008/204091388-96c142e3-52fc-41c3-84ad-48f77fe5f945.png)

![image](https://user-images.githubusercontent.com/87700008/204091502-37d5b172-d611-41f6-b716-100120af98fc.png)

Found a domain to resolve in the HTTP scan result, i.e. http://metapress.htb
![image](https://user-images.githubusercontent.com/87700008/204091652-17bace83-c0ba-4d71-ae2e-ad84e955ab55.png)

Added the domian to the host file in /etc/hosts & browsed on that website, found a regular page created with wordpress.
![image](https://user-images.githubusercontent.com/87700008/204091873-d6ed202b-d263-4ffe-852d-8b9d0edc5fdd.png)

Tried some Anonymous FTP logon but no luck :

![image](https://user-images.githubusercontent.com/87700008/204127806-544d6160-edb7-404e-82df-146efc167bb7.png)

As detected earlier the website is running on wordpress hence used a wordpress scanner using "wpscan"

![image](https://user-images.githubusercontent.com/87700008/204129169-193b393c-b1b7-4091-a1a1-2c2f92f9b8f3.png)

Since, I have used an api token (which we can get after registering in the website of wpscan) got 28 vulnerabilities :

![image](https://user-images.githubusercontent.com/87700008/204129243-c3b44c92-1432-4ea7-ae75-b9a3ba72cc7e.png)
![image](https://user-images.githubusercontent.com/87700008/204129252-6fbe354e-5662-4de1-8769-11327171ed90.png)
![image](https://user-images.githubusercontent.com/87700008/204129328-701655de-0688-4ed9-9f30-0aca6e27b233.png)


At this time got the idea that the website is vunerable to wordpress.










