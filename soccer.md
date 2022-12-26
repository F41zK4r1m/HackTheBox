   ![image](https://user-images.githubusercontent.com/87700008/209568214-13059ca8-21b0-4432-878a-613bce7c9ae0.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Initial recon :**

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
