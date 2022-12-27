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
        



