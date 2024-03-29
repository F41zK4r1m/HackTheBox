https://www.hackthebox.com/machines/Photobomb

![image](https://user-images.githubusercontent.com/87700008/198865957-aa8b5fa9-20d7-4ca0-aa91-5e6ed6674357.png)

-----------------------------------------------------------------------------------------------------------------------------------

## **Enumeration**

Initial scan showed 2 open ports, 22 & 80 :
![image](https://user-images.githubusercontent.com/87700008/198865984-222bf4f4-2122-4a32-9f8a-b1fc174847af.png)
![image](https://user-images.githubusercontent.com/87700008/198866010-64394e12-c5db-4281-8c95-0e42a770ce54.png)

Found _photobomb.htb_ from the scan & added it to the host configuration file.

Checked for the vhosts, but didn't found anything!

![image](https://user-images.githubusercontent.com/87700008/198866213-ee91de16-65fa-4d8a-9281-3aa8b53af1aa.png)

Gobuster returned the 401 results for all of the results :

![image](https://user-images.githubusercontent.com/87700008/198866218-d0c66a00-6603-49ce-89d1-9138674f22c9.png)

ffuf return the same result as well:

![image](https://user-images.githubusercontent.com/87700008/198866225-4f5fbf7f-6776-4957-873e-d1c42f0fc3f4.png)
![image](https://user-images.githubusercontent.com/87700008/198866228-16cd330b-1421-4235-a611-acede413f42c.png)

Checked for the page source & found the photobomb.js file in the configuration :
![image](https://user-images.githubusercontent.com/87700008/198866239-7f7878c6-9966-44d0-b60b-d9bd7c0ef1b3.png)

Photobomb.js file contains this user:id password structure:
![image](https://user-images.githubusercontent.com/87700008/198866251-a63ca5fe-4896-43f2-9f66-e1eb1c07c93a.png)

Tried those credentials in SSH but no luck 😕

	- http://pH0t0:b0Mb!@photobomb.htb/printer
	- pH0t0:b0Mb!@photobomb.htb

![image](https://user-images.githubusercontent.com/87700008/198867538-cf6d04d4-6bb7-4d38-80dc-295dd35e2015.png)

When checked the page hosted on http://photobomb.htb, found this page :
![image](https://user-images.githubusercontent.com/87700008/198867627-5636acce-c8fe-48ea-b276-8e284e848267.png)

The gathered credentials from the photobomb.js led to this page:
![image](https://user-images.githubusercontent.com/87700008/198867735-ee8c6088-93be-4ef0-beeb-a69a84eebc7b.png)
![image](https://user-images.githubusercontent.com/87700008/198867740-c80cbd35-8976-4abd-845e-c5d2d317d61d.png)


Now, after moved onto the page, I can access all of the pages for which previously I got the 401 response in the domain subdomain enumeration.

	- /printers:

![image](https://user-images.githubusercontent.com/87700008/198867904-de69d99f-5cb4-4206-a198-ad1bdc672206.png)

 - Html source code, looks like some service running on the port 4567 :

![image](https://user-images.githubusercontent.com/87700008/198867998-1ee570ed-edb2-4368-b6ee-5bc043ddef3a.png)

For all of the subdomains found the same html source page.

-----------------------------------------------------------------------------------------------------------------------------------

## **Initial access**

Fired the burp & checked for the responses in it. While checking for the requests checked for the download option from the page & it seems like the 'file-type' parameter is vulnerable to the command-injection.

Injected the python3 payload from pentetmonkey after the URL encode & received the connection back on my host.(pwned!🙂)

![image](https://user-images.githubusercontent.com/87700008/200121051-1b8690fa-1e2d-4ab2-8cc5-621d994ce786.png)
![image](https://user-images.githubusercontent.com/87700008/200121161-a3655009-71af-4c55-a3da-8f1ded2e69a9.png)
![image](https://user-images.githubusercontent.com/87700008/200121201-5fa44ac7-dad5-4dee-b7bd-c33994c41714.png)

And got the user flage in the 'wizard user' folder.

![image](https://user-images.githubusercontent.com/87700008/200121309-3fa7dd92-ef97-435a-b8bc-c91045cd7056.png)

-----------------------------------------------------------------------------------------------------------------------------------

## **Privlege escalation**

Nowm we can check for the sudo permissions using 'sudo -l' :

![image](https://user-images.githubusercontent.com/87700008/200168413-165cff01-1699-4bd1-934d-29fd2a8cfa46.png)

We got one cleanup script which we can run with sudo access, while checking that script found out that it's running the find command without any absolute path.

![image](https://user-images.githubusercontent.com/87700008/200168464-d807ec65-3a35-4272-875e-80794390b848.png)

We can abuse that find path & create our own find file in '/tmp' directory & change the excecution path to our own find file.

	- echo '/bin/bash'> find
	- chmod +x find

Then after creating the file we can change the execution path for that cleanup script.

	- sudo PATH=$PWD:$PATH /opt/cleanup.sh
	
Then we will run that script with sudo privlege for which we already have the access.

	- sudo /opt/cleanup.sh

After running this script we will get the sudo access immediately. (pwned!🙂)

![image](https://user-images.githubusercontent.com/87700008/200168652-b5a41bf3-5a84-490c-b546-105f9bb626d2.png)















