Hello~~ , imma going to solve the room cyborg in tryhackme hope you enjoy

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration

So first as usually i run a nmap scan to enumerate the system

![[nmap.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/nmap.png)

And find out that port 22 and 80 is open

<br>

So i visit the web server and found nothing on the default page, so i run feroxbuster and see what i can find

![[feroxbuster.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/feroxbuster.png)

And we found /admin and /etc

<br>

In /admin i found a archive file and some comment from the web site user

![[archive.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/archive.png)

So i download the file and see what i can find
<br>

And in /etc i found a passwd and squid.conf file

![[etc.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/etc.png)

![[passwd.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/passwd.png)

With the passwd file i found a user call music_archive and its password hash

<br>

## Attack/Gain Access

With the passwd file i can crack the password for music_archive using hashcat

![[crack password.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/crack%20password.png)

But when i try logging in with SSH it didn't work, so i try with other user name and it still didn't work so i just leave it and continue on

<br>

In the archive file it has a lot of file and directory


![[file content.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/file%20content.png)

and to be honest at this point of the room, i was stuck cause the file doesn't give much information and i couldn't get info

So i look up a write up and found out that this is a borg file backup, so we can use borg to view the content of the file which is encoded/encrypted

So i install borg and view the file content and mount the folder

![[borg.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/borg.png)

In the file we found alex ssh password

![[ssh password.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/ssh%20password.png)

<br>

So i login as alex and found the user flag

![[user flag.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/user%20flag.png)

<br>


## Root Access

I run sudo -l and see what i can run

![[sudo -l.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/sudo%20-l.png)

And it's a backup script, so i view the script

![[backup.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/backup.png)

Now at this point, i did some static code analysis, my first impression was that the script can be exploited by the tar command, but i found out that the /etc/mp3backups/ doesn't have write permission 

So i can't exploit it with tar. Next, i thought i can't exploit the wildcard in the script (if you don't understand what wildcard is google linux wildcard) but the wildcard is for the command find which i can't exploit

Lastly, i saw the command `getopts` and i google what it does

![[getops.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/getops.png)

![[getops 2.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/getops%202.png)

So we can supply command it the `-` syntax and execute commands, and in the end of the script, the code allow us to execute a command 

![[cmd command.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/cmd%20command.png)
<br>


So i try to exploit it by passing the command /bin/bash

![[root.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/root.png)

And bang!!, i gotten root access, but i can't get output from the system, running whoami, id doesn't output anything so i try to run a reverse shell to look at the output

With the reverse shell in place, i can see the output of the system and cat out the root flag.

![[root flag.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/root%20flag.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
That's it for the room, i also did some my own POC (Proof of concept) and see why this work

![[POC.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Cyborg/POC.png)
<br>


The reason this works because the code and a additional bracket `$()` which i assume let the system encapsulate the command and let the system run `/bin/bash` without echo getting in the way, and the dollar sign state that it's the end of the command and the system runs `/bin/bash`. Since echo is part command  that's why the system doesn't return any output, so you need to find a way to overcome that, i did it by having a reverse shell.

The statement above is my thoughts/understanding of why this work, if i misunderstand anything, please let me know. Fluffy Signout~~ ʕ•́ᴥ•̀ʔっ

