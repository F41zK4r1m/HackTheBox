  ![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/94ca46c3-f53b-47fb-8823-2dfdf6d0e413)
  https://app.hackthebox.com/machines/Pilgrimage

  ## Enumeration:

I started with performing the quick rustscan on the target to check all open ports & running services. After the completion of the scan I found 2 open ports:

  - 22 -> OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
  - 80 -> nginx 1.18.0

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0309b704-0c2f-493c-8881-8ccdeec91813)
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 20:be:60:d2:95:f6:28:c1:b7:e9:e8:17:06:f1:68:f3 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDnPDlM1cNfnBOJE71gEOCGeNORg5gzOK/TpVSXgMLa6Ub/7KPb1hVggIf4My+cbJVk74fKabFVscFgDHtwPkohPaDU8XHdoO03vU8H04T7eqUGj/I2iqyIHXQoSC4o8Jf5ljiQi7CxWWG2t0n09CPMkwdqfEJma7BGmDtCQcmbm36QKmUv6Kho7/LgsPJGBP1kAOgUHFfYN1TEAV6TJ09OaCanDlV/fYiG+JT1BJwX5kqpnEAK012876UFfvkJeqPYXvM0+M9mB7XGzspcXX0HMbvHKXz2HXdCdGSH59Uzvjl0dM+itIDReptkGUn43QTCpf2xJlL4EeZKZCcs/gu8jkuxXpo9lFVkqgswF/zAcxfksjytMiJcILg4Ca1VVMBs66ZHi5KOz8QedYM2lcLXJGKi+7zl3i8+adGTUzYYEvMQVwjXG0mPkHHSldstWMGwjXqQsPoQTclEI7XpdlRdjS6S/WXHixTmvXGTBhNXtrETn/fBw4uhJx4dLxNSJeM=
|   256 0e:b6:a6:a8:c9:9b:41:73:74:6e:70:18:0d:5f:e0:af (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBOaVAN4bg6zLU3rUMXOwsuYZ8yxLlkVTviJbdFijyp9fSTE6Dwm4e9pNI8MAWfPq0T0Za0pK0vX02ZjRcTgv3yg=
|   256 d1:4e:29:3c:70:86:69:b4:d7:2c:c8:0b:48:6e:98:04 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILGkCiJaVyn29/d2LSyMWelMlcrxKVZsCCgzm6JjcH1W
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0
|_http-title: Did not follow redirect to http://pilgrimage.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
In the port 80, I observed it's running the nginx server with version 1.18.0 & there is a domain present on that server: "pilgrimage.htb".
I added the domain to my hosts file & browsed through it.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Web Enumeration:

I then performed subdomain & DNS host enumeration on the target host, using GoBuster & FFUF.

I observed 3 sub-directories in the results:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/70ef7b65-79d7-4306-a4f5-386da3cddcbd)

Didn't found anything in the DNS host enumeration. Also, when tried to access those 3 directories but got 403 error which looks like I need to authenticate before accessing the directories.

Then I checked the website & found it's providig tool to compress image files.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0b8fdf7d-b468-4bbb-a1f6-f742c784d903)


