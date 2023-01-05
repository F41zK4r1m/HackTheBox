https://www.hackthebox.com/machines/Shoppy

![image](https://user-images.githubusercontent.com/87700008/198865004-e1a8790d-9a9a-4688-b76a-5f42aba9d457.png)


## **Initia Recon:**

Ran the scan using the Rust :

![image](https://user-images.githubusercontent.com/87700008/198864343-2470d4f6-2c90-43ce-a7e9-f06d8c4f1f60.png)

Found 3 open ports : 22,80,9093

![image](https://user-images.githubusercontent.com/87700008/198864362-a028785b-ac97-4f64-a6d2-4af39682252e.png)

Added the shoppy.htb to the hosts file.

Move on to the Subdomain enumeration :

![image](https://user-images.githubusercontent.com/87700008/198864384-2e7d3fe3-323d-461f-977a-1a8819d95189.png)

Then scanned for the VHOST :

![image](https://user-images.githubusercontent.com/87700008/198864399-cd81f773-2eef-47f7-8122-d285a8418549.png)

Here I found 1 vhost, i.e. : mattermost.shoppy.htb

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## **Exploitation:**

Now I have 2 URL's to login,

	- Mattermost.shoppy.htb/login
	- Shoppy.htb/logn

![image](https://user-images.githubusercontent.com/87700008/198864497-edd82070-1f25-46a3-8791-095bffe2bb70.png)

Tried multiple SQLi parameters but failed to login successfully. Used payloads from : https://book.hacktricks.xyz/pentesting-web/nosql-injection

Then in the page shoppy.htb, tried this parameter : **admin'||'1=1**

![image](https://user-images.githubusercontent.com/87700008/198864529-43563f95-af5f-42db-9d13-df54b2d41824.png)

After using admin'||'1=1, found that we are able to login successfully.

Found the page :

![image](https://user-images.githubusercontent.com/87700008/198864584-79fb70be-d8c8-432b-8e50-316a7e43ee18.png)

Where I tried the same parameter : admin'||'1=1, I got a Download export option. Which contains 2 users name & password hash, i.e : admin & josh

![image](https://user-images.githubusercontent.com/87700008/198864597-b7f520f6-bc2a-4ae4-8a5e-daa26f288452.png)
![image](https://user-images.githubusercontent.com/87700008/198864602-330844f7-bd12-4da6-bdbb-1432f320afc7.png)

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## **Cracking hash!**

We can use hashcat or the crackstation to crack the gathered hashes of 'josh' & 'Admin' :
Josh : 6ebcea65320589ca4f2f1ce039975995

![image](https://user-images.githubusercontent.com/87700008/198864665-11a550ca-54c5-406f-816e-d94339a9711b.png)

Cracked the Josh hash & found the password : remembermethisway
But not able to crack the Admin hash.

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## **User access:**

Now as we have the password of josh we can try the SSH login & the mattermost.shoppy.htb login

	- SSH access is denied with user josh, no luck here 😕
![image](https://user-images.githubusercontent.com/87700008/198864769-039b852b-7233-43a8-9190-ec83963f0a57.png)

	- Login on the domain mattermost.shoppy.htb, and pwn3d! 🙂
  
![image](https://user-images.githubusercontent.com/87700008/198864795-0f8cd53a-6400-4651-ab75-98b497fd554b.png)

	- While enumerating the logged in page found that there is a username password shared in the 'Deploy Machine' : 
	username: jaeger
	password: Sh0ppyBest@pp!

![image](https://user-images.githubusercontent.com/87700008/198864819-892af330-b58b-47e8-9b5c-3389e9ae4090.png)

And, now after using this credential we now successfully logged into the SSH

![image](https://user-images.githubusercontent.com/87700008/198864827-e9b6d3d1-4d25-4ac5-abd5-b90933699f97.png)
![image](https://user-images.githubusercontent.com/87700008/198864828-2dea1297-faf0-4753-9d50-fff0e450003b.png)

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## **Root access**

Did some manual recon & executed sudo -l & found this :

![image](https://user-images.githubusercontent.com/87700008/198864856-c05441e8-f01a-46e7-b2be-213b25c78946.png)

Checked the executable file in text format & found this :
![image](https://user-images.githubusercontent.com/87700008/198865217-154c0eb6-2a4e-4071-a365-ba0d549c0765.png)

By looking at the extracted content found that the file contains josh password manager password, i.e : **Sample**

By using the gathered credentials logged into the josh password manager :
![image](https://user-images.githubusercontent.com/87700008/198864907-d1a8466c-077f-48d4-a5f7-c37bc3cd6996.png)


And, got another credentials :

	- username: deploy
	- password: Deploying@pp!

Switched user to deploy & checked for the running processed using 'top' command but didn't found any which means we are in a docker container right now:

![image](https://user-images.githubusercontent.com/87700008/198864937-981aa297-76ff-4d5f-9657-02c4e1ebb6cd.png)
![image](https://user-images.githubusercontent.com/87700008/198864942-1d37d659-79bd-4780-ab6d-552986830750.png)

Now, we are aware that we are in a docker container we can run the docker priv esc command from the gtfo bins :

![image](https://user-images.githubusercontent.com/87700008/198864949-7a7a8cbf-16de-4870-9691-d36f134870e2.png)
![image](https://user-images.githubusercontent.com/87700008/198864955-57381d66-e40c-4959-b931-db8249b95f64.png)

Now, after running we just spawned the root shell & escaped the docker container.


















