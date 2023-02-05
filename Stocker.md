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
