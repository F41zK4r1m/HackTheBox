![image](https://user-images.githubusercontent.com/87700008/216767029-d26d40d7-7fdc-4b53-9ee0-44dae9dcd7e2.png)
https://app.hackthebox.com/machines/Stocker

## Enuemration
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### Rustscan

Started with a quick Rustscan :
    
    sudo rustscan -a 10.10.11.196 -- -sC -sV -T4 -vv -oN stock_nmap
    
![image](https://user-images.githubusercontent.com/87700008/216767718-0db8e9bb-6c05-4649-b468-1cc06330b439.png)
![image](https://user-images.githubusercontent.com/87700008/216767748-104c9044-47f1-40a6-b960-52d0b9182a7d.png)

After scan I only found 2 open ports, i.e. :

    22 - open  ssh  syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
    80 - open  http syn-ack ttl 63 nginx 1.18.0 (Ubuntu)

Also, found a re-direction towards the domain : http://stocker.htb, added this redirection to the /etc/hosts file.

After adding browsed through the website & a regular website which is still under developement.

![image](https://user-images.githubusercontent.com/87700008/216768868-8f1e54cd-770a-453e-b219-499d2a486f41.png)

Ran GoBuster for the subdomain enumeration but didn't found anything helpful.

![image](https://user-images.githubusercontent.com/87700008/216770773-3772af77-1945-4fe4-bfff-493267e80d51.png)

Then moved ahead to the vhost enumeration using GoBuster but didn't got any of the result as well.😕

After failed to find any intitial access vector in the website & the subdomain enumeration I thought of finally giving a try with "FFUF" for vhsot enumeration & subdomain enumeration & I finally got a new vhost from the scanning result.

        dev.stocker.htb
            
 ![image](https://user-images.githubusercontent.com/87700008/216813569-d2c5b89e-1577-4c23-9cff-ece3ae42c589.png)
 
 Added the newly found domain in the /etc/hosts config file & opened the link in the browser & found a login page on the screen.
 
 ![image](https://user-images.githubusercontent.com/87700008/216983026-cf7b50cb-81d2-4f01-aa45-c7f5f018b446.png)

Also, ran a sub-domain enumeration again on this new website but didn't found any useful subdomain.

![image](https://user-images.githubusercontent.com/87700008/216983828-50030a54-0ee9-415b-a888-d8eb6577734f.png)

In this login page I tried different SQLi payloads but none of them worked, then I moved on to try the NoSQL payloads from the hactrickz website.
Tried this json payload & got the 200 response :

        {"username": {"$ne": null}, "password": {"$ne": null} }
        
Also, the Content-Type needs to be changed to **Content-Type: application/json** because we are using the json syntax. 

![image](https://user-images.githubusercontent.com/87700008/216998904-32d82da9-e07b-4fef-b12d-be03834f17b0.png)

Followed the re-direction & landed onto this website : "dev.stocker.htb/stock"

![image](https://user-images.githubusercontent.com/87700008/216999280-5e10af59-42ea-48e7-b458-89b7d5dcd4cc.png)

When checked the source code of the page found that there is an api request which is performing the POST request with the order id.

![image](https://user-images.githubusercontent.com/87700008/217033636-ca8ddfe3-b32f-4cde-ae0a-deb064c737cb.png)

I then performed some manual enumeration & orderd something on the website & intercepted the requests with Burp.
I observed from the proxy requests that the order ID which is appeared in the burp proxy is also visible in the final URL of the PDF result.

![image](https://user-images.githubusercontent.com/87700008/217039578-9de5b1b3-5dd2-4e0a-bb3a-a98c3f618c0e.png)
![image](https://user-images.githubusercontent.com/87700008/217039660-14bd53d2-700d-4932-86e0-52569eda1d88.png)

Which means that the PDF is working dynamically, so I took some help from the hint & found an exploit related to [Dynamic PDF](https://book.hacktricks.xyz/pentesting-web/xss-cross-site-scripting/server-side-xss-dynamic-pd).

