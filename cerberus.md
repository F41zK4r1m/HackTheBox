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
