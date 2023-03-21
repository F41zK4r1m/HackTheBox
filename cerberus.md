  ![image](https://user-images.githubusercontent.com/87700008/226189420-afa389ca-2fb9-4439-8223-14edf49a43f2.png)

https://app.hackthebox.com/machines/534

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration :

Started with a quick rustscan :

    sudo rustscan -a 10.10.11.205 -- -sC -sV -T4 -vv -oN cerberus_nmap
    
After scanning I found only 1 open port '8080'.

```
PORT     STATE SERVICE REASON         VERSION
8080/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.52 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Did not follow redirect to http://icinga.cerberus.local:8080/icingaweb2
```

In the port scan results I found a domain, running on port 8080 : "icinga.cerberus.local:8080". So I added the domain to /etc/hosts file.

Next I tried UDP scanning on the box to collect some for info & I found 3 more open ports : 53, 88, 123.

```
Nmap scan report for icinga.cerberus.local (10.10.11.205)
Host is up, received echo-reply ttl 127 (0.17s latency).
Scanned at 2023-03-20 13:10:31 EDT for 19558s
Not shown: 997 open|filtered ports
Reason: 997 no-responses
PORT    STATE SERVICE      REASON               VERSION
53/udp  open  domain       udp-response ttl 127 (generic dns response: NOTIMP)
| fingerprint-strings: 
|   NBTStat: 
|_    CKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
88/udp  open  kerberos-sec udp-response         Microsoft Windows Kerberos (server time: 2023-03-20 17:25:46Z)
123/udp open  ntp          udp-response         NTP v3
| ntp-info: 
|_  receive time stamp: 2023-03-20T18:47:26
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port53-UDP:V=7.91%I=7%D=3/20%Time=6418970F%P=x86_64-unknown-linux-gnu%r
SF:(DNSStatusRequest,C,"\0\0\x90\x04\0\0\0\0\0\0\0\0")%r(NBTStat,32,"\x80\
SF:xf0\x80\x82\0\x01\0\0\0\0\0\0\x20CKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\0\0!\
SF:0\x01");
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 13s
```
