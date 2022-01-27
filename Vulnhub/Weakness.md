Hello Everyone today i'm doing a write up for weakness the Vulnhub box, hope you guys enjoy.

---

## Recon
First i run a port scan on the box 
```bash
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 de:89:a2:de:45:e7:d6:3d:ef:e9:bd:b4:b6:68:ca:6d (RSA)
|   256 1d:98:4a:db:a2:e0:cc:68:38:93:d0:52:2a:1a:aa:96 (ECDSA)
|_  256 3d:8a:6b:92:0d:ba:37:82:9e:c3:27:18:b6:01:cd:98 (ED25519)
80/tcp  open  http     Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
443/tcp open  ssl/http Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
| ssl-cert: Subject: commonName=weakness.jth/organizationName=weakness.jth/stateOrProvinceName=Jordan/countryName=jo
| Not valid before: 2018-05-05T11:12:54
|_Not valid after:  2019-05-05T11:12:54
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|_  http/1.1
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
Which have 3 ports open 22,80,443

<br>


So i started looking at port 80 and run a scan on port 443 on feroxbuster (-k is needed to ignore ssl cert).

```bash
feroxbuster -u https://10.0.2.22/ -w /opt/wordlist.txt -t 200 -x php,html,txt -k
```

The default index.html is just a default Apache page so i turn to port 443 and run a feroxbuster scan on port 80.

```bash
feroxbuster -u http://10.0.2.22/ -w /opt/wordlist.txt -t 200 -x php,html,txt 
```


<br>

The Port 443 Scan return us this
```bash
301        9l       28w      307c https://10.0.2.22/blog
301        9l       28w      307c https://10.0.2.22/test
301        9l       28w      310c https://10.0.2.22/uploads
200        9l       16w      216c https://10.0.2.22/upload.php
```

<br>


Port 80 Scan Result
```bash
301        9l       28w      305c http://10.0.2.22/blog
301        9l       28w      305c http://10.0.2.22/test
301        9l       28w      308c http://10.0.2.22/uploads
200        9l       16w      216c https://10.0.2.22/upload.php
```
Port 80 scan also return the same result so i assume that both are the same site just on different ports.

<br>

![](https://i.imgur.com/n5kVE3H.png)

![](https://i.imgur.com/yKBkl3E.png)

Blog and Uploads Just return a empty directory.

![](https://i.imgur.com/pMOVzFP.png)

Test return us a picture, which state that we need more key, its kinda a hint but not really.


<br>

So, next i headed to upload.php 
![](https://i.imgur.com/MJxpTbI.png)


![](https://i.imgur.com/stRmuon.png)

I guess that, the string in the page is base64 so i went to cyberchef.

![](https://i.imgur.com/9xWOz0S.png)

<br>


My guess right now is that the website might execute script that we give them or we might be able to upload a php reverse shell and gain access.

But after trying everything i hit a brick wall, the uploads doesn't work and burpsuite and ZAP was returning nothing, so i started to think about this phase that is in the webpage source code.

![](https://i.imgur.com/vyIkVSB.png)

I was thinking that maybe the uploads page is just a trick and it doesn't do anything, and around that time i notice a host name in the nmap scan **weakness.jth**.

```bash
commonName=weakness.jth/organizationName=weakness.jth/stateOrProvinceName=Jordan/countryName=jo
```

So i added the host name into my hosts file and visit the host.

![](https://i.imgur.com/dr9wWco.png)

<br>


And i found a new page, bingo so i fire off another feroxbuster scan 

```bash
200       30l       72w      526c http://weakness.jth/index.html
301        9l       28w      314c http://weakness.jth/private
200        1l        3w       14c http://weakness.jth/robots.txt
301        9l       28w      321c http://weakness.jth/private/assets
301        9l       28w      325c http://weakness.jth/private/assets/css
301        9l       28w      320c http://weakness.jth/private/files
200       44l       77w      989c http://weakness.jth/private/index.html
301        9l       28w      324c http://weakness.jth/private/assets/js
```

![](https://i.imgur.com/26BLxeA.png)

The robots.txt page is just a joke XDD.

And the private directory we found 2 files a public ssh key and a notes.txt

### Notes.txt
![](https://i.imgur.com/y2kAoqJ.png)

### mykey.pub
![](https://i.imgur.com/3GGtUFn.png)


<br>

## Exploitation

So i search the version number and found a exploit revolving around the predictable random number generator in the keygen function in linux. 

![](https://i.imgur.com/IttgfQi.png)


So i download the zip file in exploitDB and unzip it in a directory. Since we have the public key value i just grep the files in the directory and found a match on the public key.

![](https://i.imgur.com/SiqdTPz.png)


So we now have the private key of a user in the box, but when i try to login with root it doesn't work and i was stuck here for quite a while but then when i was backtracking i found that the index.html of weakness.jth have a *-n30* beside the bunny.

![](https://i.imgur.com/rJEBnsH.png)


So i use that username and got into the system.

![](https://i.imgur.com/xPekSn4.png)



<br>

## Priv-Esc

There is a code file in the n30 home directory, which is a python complied program. 

![](https://i.imgur.com/VBjewTs.png)


I ignore that and enumerate around the system abit and found out that we have sudo privilege, so we just need n30 password to gain root access.

![](https://i.imgur.com/U0pw0Vu.png)

So i mess around with the code program and google Python Decomplie and found a library for it. [Decompiler.](https://securityonline.info/uncompyle-python-bytecode-decompiler/)

After installing the require library and decompile the program, by adding .rpc to the end of code program (code.rpc). We got n30 password.

```python
inf += chr(ord('n'))
inf += chr(ord('3'))
inf += chr(ord('0'))
inf += chr(ord(':'))
inf += chr(ord('d'))
inf += chr(ord('M'))
inf += chr(ord('A'))
inf += chr(ord('S'))
inf += chr(ord('D'))
inf += chr(ord('N'))
inf += chr(ord('B'))
inf += chr(ord('!'))
inf += chr(ord('!'))
inf += chr(ord('#'))
inf += chr(ord('B'))
inf += chr(ord('!'))
inf += chr(ord('#'))
inf += chr(ord('!'))
inf += chr(ord('#'))
inf += chr(ord('3'))
inf += chr(ord('3'))
```

```bash
n30:dMASDNB!!#B!#!#33
```

So i just run sudo bash and gain root.

![](https://i.imgur.com/wduqX96.png)

---
I think that the box is great and i like it alot, cause you don't always have to rely on "try and try harder" method. The Key exploit part and the method for priv-esc i also like it alot, its on the easier side but i think it's quite creative props to the creator. So that it's for the weakness box, i hope that you enjoy it. Fluffy Signout~~ ʕ•́ᴥ•̀ʔっ
