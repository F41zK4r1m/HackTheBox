 ![image](https://user-images.githubusercontent.com/87700008/221355489-6f404be9-6582-460a-aa3d-ecc3eaa1392b.png)

https://app.hackthebox.com/machines/Investigation

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
Enuemration :

I started with q qucik rustscan & observed only 2 open ports on the network, i.e 22 & 80

![image](https://user-images.githubusercontent.com/87700008/221355635-38b21308-4d60-427d-b5c4-ec39f8a8f594.png)

    - 22 open ssh syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
    - 80 open http syn-ack ttl 63 Apache httpd 2.4.41
    
In the port scan results I found a domain as well 'http://eforenzics.htb/' , which I added to /etc/hosts file.
Browsed through the website & found an upload image options for forensics results.

![image](https://user-images.githubusercontent.com/87700008/221406576-cc1c2a20-e482-493c-ba17-fc0e2918370c.png)
![image](https://user-images.githubusercontent.com/87700008/221406588-b3dc4bfe-c879-4ec7-8eab-9c42c94accf1.png)

Then I proceeded with the subdomain scanning of the webserver & found only 1 result in it '/assets'

![image](https://user-images.githubusercontent.com/87700008/221356009-866a66d0-d4ff-4de4-a1b4-e9dd65dca4bd.png)

I then tried to accessing the '/assets' but got 403 error.

Then I tried to upload the .php file in hope to get some shell but the filters are blocking the upload other than .jpg & .png.
I tried many filters to bypass but it wasn't working.

![image](https://user-images.githubusercontent.com/87700008/221406739-726f6087-f029-4b28-9743-1de130451c22.png)

Then after multiple failure I uploaded regular PNG image & it gave the the forensics results of the image.

![image](https://user-images.githubusercontent.com/87700008/221406805-c9dc7b77-bec4-4054-8635-c1008c84b7bb.png)

I observed that the website is using the 'ExifTool ver 12.37' for the image forensics purpose.

![image](https://user-images.githubusercontent.com/87700008/221406990-c08f0376-9fcc-4a3b-ab76-fdbedf912943.png)
