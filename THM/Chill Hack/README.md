# Chill Hack	

Hello, today i'm gonna solve the room chill hack from tryhackme. Hope you enjoy

----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration

I first use nmap to see what ports are open

![nmap_scan](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/nmap.png)

Port 21,22,80 is open
<br>
So i first tackle the HTTP web server and use feroxbuster to see if anything is hiding, and i found the secret directory

![[secrect page.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/secrect%20page.png)

<br>

## Command Inject and Bypass 

The input box seems like a input terminal so i try whoami and id, and it reply with www-data. So i know that this is link with the terminal

But commands like nc, python, ls, perl, bash are ban and the system will not let through

i also connect to the FTP services and found a note, which let us know there are atleast 2 user in the system

![[note.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/note.png)

![[fail attempt.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/fail%20attempt.png)


<br>
so in the end i find out that () will let the system ignore text in the () so i create a reverse shell

![[successful attempt.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/successful%20attempt.png)


![[connection got.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/connection%20got.png)

The Obfuscated bash command reference : https://pentestbook.six2dez.com/exploitation/reverse-shells
<br>

## Command Injection Part 2

After i got in, i run sudo -l to see what service i can run and if i can access the user folder

![[sudo.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/sudo.png)


And we can run sudo with the user apaar for the .helpline.sh

And the bash file contains


![[helpline.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/helpline.png)

So it's another command injection challenge 

So i run /bin/sh to gain a shell

![[bin sh.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/bin%20sh.png)

with that i cat out the user flag

![[Pic/Chill Hack/user flag.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/user%20flag.png)

<br>

## Root Access/ Privilege Escalation  
I also created another reverse shell for the user apaar
but in the end, i found nothing interesting with the user, so i back track and look at the html files and i found hacker.php 

![[hacker pic.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/hacker%20pic.png)

So i cat out the file, and the file states that look inside the darkness which kinda insinuate stenography so i downloaded the hacker pic and use steghide to see if anything is in the pic

And a backup.zip is inside the pic

![[backup zip.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/backup%20zip.png)

They zip file is encrypted so i use a zip cracker tool from github and found the password


![[zip password.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/zip%20password.png)

and zip file contain a source code php which contain a hard coded credential in base64 

![[base 64.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/base%2064.png)

![[ssh password.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/ssh%20password.png)

So i try to SSH with this password and the user anurodh worked

![[login.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/login.png)

So i run linpeas and found that he has a docker ID

![[docker id.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/docker%20id.png)

So i just run the command from a reference i have to exploit the system

![[priv esc.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/priv%20esc.png)

Reference : https://book.hacktricks.xyz/linux-unix/privilege-escalation#writable-docker-socket



And bang i have the root folder in mnt and access the root flag

![[Pic/Chill Hack/root flag.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Chill%20Hack/root%20flag.png)

----------------------------------------------------------------------------------------------------------------------------------------------------------------------
So that's it for the room, i would say the room should be in the medium difficulty, but the room was super fun and hope you guys enjoyed it as well Fluffy Signout~~ ʕ•́ᴥ•̀ʔっ



#Chill_Hack
