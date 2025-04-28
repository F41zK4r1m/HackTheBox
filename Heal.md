![image](https://github.com/user-attachments/assets/56164e5a-5882-4376-9523-ff51c71a4562)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Enumeration

## Port scan:

I started the enumeration phase by performing a full ports and services scan on the target machine using `nmap`. The scan revealed only two open ports:

```
sudo nmap -p- 10.10.11.46 -A -v -oN heal_nmap
```

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 68:af:80:86:6e:61:7e:bf:0b:ea:10:52:d7:7a:94:3d (ECDSA)
|_  256 52:f4:8d:f1:c7:85:b6:6f:c6:5f:b2:db:a6:17:68:ae (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://heal.htb/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
Device type: general purpose
Running: Linux 5.X
OS CPE: cpe:/o:linux:linux_kernel:5.0
OS details: Linux 5.0
Uptime guess: 6.307 days (since Sun Apr 20 21:35:33 2025)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=261 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

From the results, I identified a domain `heal.htb`, which I then added to my `/etc/hosts` file for easier access during further enumeration.

## Web Enumeration

After completing the port scan, I moved on to web enumeration by browsing the website hosted on `heal.htb`. The site appeared to offer resume-building services:

![image](https://github.com/user-attachments/assets/3671f808-fe21-48a5-9bce-4c6955c17204)

I initially attempted some common credentials like `admin:admin`, but they were unsuccessful. I then tested a few basic SQL injection payloads; however, they also did not yield any results.

Next, I conducted a subdirectory scan of the application using `dirsearch`, but it didn’t uncover anything noteworthy:

```
dirsearch -u http://heal.htb -x 403,404,402 --crawl -r
```

![image](https://github.com/user-attachments/assets/9f7d9675-e281-4a28-be6d-d3e4e1eabb0e)

Afterward, I registered a new account on the website. During the registration, I noticed that it required filling out details to generate a resume:

![image](https://github.com/user-attachments/assets/6edeb430-bb6f-443f-85e8-50deaec8eea4)

I launched Burp Suite to intercept and analyze the traffic while proceeding with the resume creation. Upon filling in all the required fields, I received a generated PDF containing my submitted information:

![image](https://github.com/user-attachments/assets/a3e66598-70d7-4460-9cf0-c4aefc815263)

Additionally, while exploring the site, I found a "Survey" option. Clicking on it revealed another subdomain: `take-survey.heal.htb`. I added this new domain to my `/etc/hosts` file for further enumeration.

### Subdomain Enumeration

Moving forward, I scanned the application to identify any potential subdomains. I used `ffuf` for this purpose:

```
ffuf -H "Host: FUZZ.heal.htb" -u http://10.10.11.46/ -w /usr/share/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt
```

The scan revealed a subdomain: `api.heal.htb`. I promptly added it to my `/etc/hosts` file.

![image](https://github.com/user-attachments/assets/43835f87-31ef-49b2-9017-67f04d11a357)

Upon browsing to `api.heal.htb`, I was greeted by a page displaying Ruby application details and version information:

![image](https://github.com/user-attachments/assets/ba8dfe4f-b7c7-41d0-8c68-86904d137490)

I proceeded to perform subdirectory enumeration against this subdomain using both `ffuf` and `dirsearch`, but neither uncovered any useful endpoints to interact with the application:

![image](https://github.com/user-attachments/assets/c5a61642-a403-4b7f-b9c5-34bb4d0939f6)

The responses suggested that access to resources required some kind of token, most likely an authorization token:

![image](https://github.com/user-attachments/assets/3bf83572-e04d-4bd1-bdbb-bc5c08772cac)

### Survey Application

After enumerating the `api` subdomain, I moved on to explore the `take-survey.heal.htb` page. As expected, it displayed a basic survey with a single question:

![image](https://github.com/user-attachments/assets/39bd8823-10b8-4eee-9b27-6c7e76ee0df0)

I proceeded to perform directory enumeration on the survey page as well. This revealed a few interesting endpoints, notably `/Admin` and `/editor`:

![image](https://github.com/user-attachments/assets/9089b80d-b3cc-430d-8f1b-23cf22ea45ca)

- Accessing **/editor** returned a 403 Forbidden response.
- Accessing **/Admin** displayed a login page:

![image](https://github.com/user-attachments/assets/43cbaa10-3040-4df1-8b78-5b81ba3cfb5c)

## LFI (Local File Inclusion)

After identifying additional endpoints and capturing all requests with Burp Suite, I started looking for ways to further explore the application.

Initially, I noticed a **JSON Web Token (JWT)** embedded in the requests related to the PDF generation process. This hinted that the token might also be valid for accessing the API resources:

![image](https://github.com/user-attachments/assets/0c8fffeb-b5d3-470a-ba9d-1fd958fe031b)

I added an `Authorization` header with the extracted token, and it successfully authenticated my requests. Decoding the token revealed the following structure:

![image](https://github.com/user-attachments/assets/4559792c-82c1-4c12-b115-cec2e230a188)

I tried modifying the `user_id` inside the token to test for IDOR (Insecure Direct Object Reference) vulnerabilities. However, the application responded with "**invalid token**", indicating that the token’s integrity was being verified—likely signed with a server-side secret—preventing tampering.

Next, while reviewing the HTTP requests, I noticed a **GET** request used to download the generated PDF. I tested for Local File Inclusion (LFI) by replacing the filename parameter with `/etc/passwd`, and it worked:

![image](https://github.com/user-attachments/assets/8d410848-5854-42d2-9129-3c0a2b31de12)

### Targeting Sensitive Ruby Configuration Files

Since the API server banner indicated a **Ruby** application, I researched common sensitive files in Ruby environments that could be exposed:

- config/credentials.yml.enc: Encrypted secrets (API keys, database passwords, etc.).
  - config/master.key: Required to decrypt credentials.yml.enc. If exposed, attackers can decrypt all secrets.
- config/database.yml: Database credentials (e.g., PostgreSQL, MySQL).
- .env or config/.env: Environment variables (often contains plaintext secrets in development).

I attempted to access `config/database.yml`, and successfully retrieved it. It revealed the path to a SQLite database:

![image](https://github.com/user-attachments/assets/8da83390-885c-452b-8baa-75fe98dfe54e)

Following this, I browsed to the SQLite database file location and successfully dumped the content. It contained a list of **users and their password hashes**:

![image](https://github.com/user-attachments/assets/9f261861-5214-4e26-834d-0d5879a1e57f)

To make analysis easier, I downloaded and opened the SQLite dump in a browser, displaying the user data in a much cleaner format:

![image](https://github.com/user-attachments/assets/e2a2193c-8243-4bee-b20b-a07f91c4dfb5)

### Hash crack:

After retrieving the SQLite database, I targeted the hash for the user `ralph`, who appeared to have administrative privileges according to the database records.

To identify the hash type, I used `haiti`, which revealed that the hash was in `bcrypt` format:

![image](https://github.com/user-attachments/assets/75fc6e3b-16a0-404f-85bc-558fe517392a)

I then used **John the Ripper** with the popular `rockyou.txt` wordlist to attempt cracking the bcrypt hash. The command used:

```
john --w=/usr/share/wordlists/rockyou.txt --format=bcrypt bcrypt_hash_file
```

Within less than **30 seconds**, John successfully cracked the hash, revealing the clear-text password:

![image](https://github.com/user-attachments/assets/d03ef97c-7498-403d-b945-5bc5ebec4729)

Initially, I attempted to use these credentials over **SSH**, but the login failed. However, when I used them to log in to the **Survey** application's **admin panel**, it worked successfully:

![image](https://github.com/user-attachments/assets/1ee1b003-b3ce-4b2f-b510-9c2c655a2ae1)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Initial access

## LimeSurvey RCE Exploitation

After logging into the **LimeSurvey** application as an administrator, I thoroughly enumerated the available functionalities but didn’t immediately find a misconfiguration that would allow direct access to the underlying system.

I decided to research known exploits for the **LimeSurvey** application and found a relevant Proof-of-Concept (PoC) exploit on [GitHub](https://github.com/Y1LD1R1M-1337/Limesurvey-RCE), which leverages the plugin system to achieve **Remote Code Execution (RCE)** by uploading a custom malicious plugin.

I cloned the repository and attempted to run the provided exploit script using the administrator credentials:

![image](https://github.com/user-attachments/assets/36237460-0829-423c-a9b5-d8f2f8766ae7)

However, the automated exploit failed to achieve RCE, so I decided to manually adapt and upload the malicious plugin.

I uploaded the malicious ZIP file from the exploit repository:

![image](https://github.com/user-attachments/assets/b09bf948-ed20-44de-ba02-e169e666fd3c)

The upload initially failed due to compatibility issues:

![image](https://github.com/user-attachments/assets/ac0f6e3f-6a14-4284-8c81-657047024385)

To fix this, I made the following changes:

- Updated the reverse shell script (`php-rce.php`) to point to my own listener IP address.
- Modified the `config.xml` to ensure compatibility with the LimeSurvey version in use by adding:


```
....
....
    <compatibility>
        <version>4.0</version>
        <version>5.0</version>
        <version>6.0</version>
    </compatibility>
    <updaters disabled="disabled"></updaters>
</config>
```

After making these adjustments, I successfully uploaded the plugin through the Plugins section:

![image](https://github.com/user-attachments/assets/1a4ad18e-dbc2-4f33-9ea3-91d0a35e3592)

Installed the plugin:

![image](https://github.com/user-attachments/assets/fcc5feae-1ecc-4f8b-bc70-c5348ced837f)

And activated it:

![image](https://github.com/user-attachments/assets/8845973b-b487-474f-856b-df430e8bf05a)

Finally, I started a **netcat** listener and navigated to the reverse shell PHP file at:

```
/upload/plugins/Y1LD1R1M/php-rev.php
```

As soon as the file was executed, I received a reverse shell connection back as the `www-data` user:

![image](https://github.com/user-attachments/assets/b7ab7ada-d9bf-4f8b-8029-1cee2991315f)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Lateral movement

## www-data -> Ron

After obtaining the initial reverse shell as `www-data`, the first step I took was to stabilize the shell for better interaction:

```
python3 -c 'import pty;pty.spawn("/bin/bash")'

export TERM=xterm

background shell (ctrl+z)

stty raw -echo; fg
```

Once the shell was stabilized, I began basic enumeration. In the `/home/` directory, I found two user accounts:

- ralph
- ron

I did not have permission to access either user's directory at this point.

Since manual enumeration was limited, I uploaded and executed `linpeas.sh` to automate privilege escalation checks. During the scan, I discovered **cleartext** credentials stored in a configuration file (`config.php`):

![image](https://github.com/user-attachments/assets/a1c4b249-ea47-41ba-9a05-8146570c142c)

I initially attempted to authenticate via SSH using ralph's account, but it failed. Trying the same credentials with ron was successful, and I obtained an interactive SSH session:

![image](https://github.com/user-attachments/assets/28b4c7b7-3406-4b81-bbd8-6ed571db9d56)

From there, I was able to retrieve the user flag! 🎯

## Ron -> Root

After gaining access as `Ron`, I began enumerating for potential paths to escalate privileges to root.

First, I checked **sudo privileges**:

```
sudo -l
```

![image](https://github.com/user-attachments/assets/0a38cf76-4017-4879-bcf1-5dd75ac98699)

No sudo privileges were assigned to Ron.

Next, I looked for SUID/SGID binaries that might be vulnerable:

```
find / -type f -perm -u=s 2> /dev/null
```

![image](https://github.com/user-attachments/assets/b30e7e59-8194-46f3-932c-c47bdea8d5e2)

Nothing interesting or obviously exploitable was found here.

Then, I checked for scheduled cron jobs that might be misconfigured:

![image](https://github.com/user-attachments/assets/479d0d93-89e6-4025-92f6-b3c9411aa9d5)

No custom or exploitable cron jobs were observed.

Since manual checks weren't revealing a clear path, I decided to run `linpeas.sh` again under Ron’s context to perform a deeper privilege escalation enumeration.

### Port forwarding

Since the `linpeas` scan didn't reveal a clear privilege escalation path, I moved on to manually inspect locally available ports on the target system using:

```
ss -tunlp
```

![image](https://github.com/user-attachments/assets/ce3a221f-f20b-41a5-b6aa-a0dd8106f237)

From previous enumeration, I already knew that ports **3000** and **3001** were being used by the Resume API and Survey application respectively.

However, to investigate the other open ports, I started **cURL** probing each local service manually.
While doing so, I discovered that port **8500** was serving a `HashiCorp Consul` web interface:

![image](https://github.com/user-attachments/assets/d749176f-2d68-45dd-8905-9dc737d8f737)

To interact with the Consul web portal from my local machine, I established an **SSH port forwarding** tunnel:

```
ssh -L 8500:127.0.0.1:8500 ron@heal.htb
```

This setup forwarded the target's localhost `8500` port to my local machine, allowing me to browse Consul at:

![image](https://github.com/user-attachments/assets/e4c4e112-0d43-4c7a-b9ab-fefe1d386482)

### Consul exploit

After gaining access to the **Consul** portal (version 1.19.2), I searched online for known exploits related to this version.

I found a promising RCE exploit listed on [Exploit-DB](https://www.exploit-db.com/exploits/51117), which allows gaining a reverse shell via Consul’s exposed HTTP API.

I downloaded and ran the Python exploit script with the necessary arguments, while setting up a netcat listener on my machine.

However, on the first attempt, the exploit failed — it requested an `ACL token` which I didn't possess:

![image](https://github.com/user-attachments/assets/cd6dbcaa-3579-4d2e-999c-46cd8e8f1d46)

On retrying the exploit with an empty ACL token, it successfully executed:

![image](https://github.com/user-attachments/assets/329d0e0e-95cc-4797-a257-d50db7a1299e)

As a result, I quickly received a reverse shell as the **root** user!

I then fetched the `root.txt` flag and completed the box.(Pwned!🎉)🙂

![image](https://github.com/user-attachments/assets/448fec57-5916-4e0d-a2c9-e1fc171ce009)
