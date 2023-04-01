  ![image](https://user-images.githubusercontent.com/87700008/228204118-767d13c9-a55e-4531-8f1b-330d9d209a2b.png)
  
https://app.hackthebox.com/machines/Socket

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration :

I started with the quick rustscan & found 3 open ports in the machine :

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

From port 80 I observed a domain "http://qreader.htb/". I added this domain to /etc/hosts file.

I then moved onto the website & found that it's taking the QR code as Input & showing the embedded text present in the QR.

![image](https://user-images.githubusercontent.com/87700008/229103303-772de251-d144-473b-b6bc-57aac382dba7.png)

I checked for the sub-directories & found 3 open directories in the results :

![image](https://user-images.githubusercontent.com/87700008/229103939-cb6cb1dc-4514-4ed6-9b8e-aa0e7075cadb.png)

Nothing special in the sub-directories as well.

In our port scan result we observed an open port "5789" where WebSocket is open, so to enumerate that web socket I downloaded this [script](https://github.com/PalindromeLabs/STEWS) from GitHub.

    python3 STEWS-vuln-detect.py -1 -n -u 10.10.11.206:5789
    
![image](https://user-images.githubusercontent.com/87700008/229136259-3425ae45-de4b-4e02-bb33-35fa50066725.png)

After the scanning we found a vulnerable directory at : "ws://qreader.htb:5789", but it's not much helpful.

In the website there is a download app option available for windows & Linux, so I downloaded the Windows executable from the website.

![image](https://user-images.githubusercontent.com/87700008/229138351-ebfe702d-2b82-4002-a7fa-9ab283497acd.png)

After downloading the binary I decompiled it using [pycdc](https://github.com/zrax/pycdc/) & found this code in it :

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

In the code I found a socket domain "ws.qreader.htb" to connect on & I added it to the hosts file.
