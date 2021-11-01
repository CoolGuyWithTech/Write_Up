Hello Everyone, today i'm going to solve the room Year of the Rabbit from tryhackme.

_______________________________________________________________________________________________________________________________________________________________________

## Recon
So first, i run a nmap scan on the system

![[Pic/Year_Of_The_Rabbit/nmap.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/nmap.png)

*Port 21,22 and 80 is open*

<br>

## Web Server Active Discovery

I saw that ftp is open so i try anonymous login, but anonymous login didn't work

![[ftp anonymous fail.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/ftp%20anonymous%20fail.png)

So i try feroxbuster next to try to uncover additional directory

![[feroxbuster.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/feroxbuster.png)

and it found a assert directory, in the directory it also contain a css file which contain a comment
<br>
![[secrect.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/secrect.png)
<br>
when i go to the directory it will redirect us to the famous Rick Astley music Never Gonna Give You Up (i've gotten Rick Roll)

![[source page secrect.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/source%20page%20secrect.png)

And the source code for the site is this

So i open up ZAP to intercept the traffic to see if i can discover more info and i found this request, which point to another directory

![[another directory.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/another%20directory.png)

And found a picture of a Hot Babe

![[hot babe.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/hot%20babe.png)

So i downloaded the photo, like a normal person XD

## Stenography

I run exiftool and steghide of the image and found nothing, so i use binwalk and found that it contains compess data.

![[Pic/Year_Of_The_Rabbit/binwalk.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/binwalk.png)
<br>
I decide to strings the image and found the ftp user login and a list of possible password


![[stings.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/stings.png)

<br>

So i use medusa to brute force the login using the custom list of password and found the account

![[Pic/Year_Of_The_Rabbit/medusa.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/medusa.png)

![[ftp found.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/ftp%20found.png)

<br>

## Decode

In the ftp share we find Eli ssh credential 

![[credential.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/credential.png)

So its encoded in Brainfuck so we decode it and got the user name and password

![[eli crendential.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/eli%20crendential.png)

<br>

## SSH

I ssh into eli and found this message

![[ssh message.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/ssh%20message.png)

So i use the find command and search for the directory. (I would recommend you to try the room **the find command** in tryhackme if you haven't, it's very useful for this kind of situation)

![[find.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/find.png)

In the directory we found Gwen Credential not encoded

![[gwen crendential.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/gwen%20crendential.png)

So i login in a gwen and got the user flag

![[Pic/Year_Of_The_Rabbit/user flag.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/user%20flag.png)

<br>

## Root

So now i run sudo -l to see what i can run to gain root access

![[sudo -l.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/sudo%20-l.png)

So we can run vi with sudo. In this part i struggle for a long-long time cause all exploit under the sun doesn't work, so i look up a write up and they hint at **CVE-2019-14287** so i research on it and found out that the privilege escalation is not practically related to Vi.  you need to run `sudo -u#-1 /usr/bin/vi /home/gwendoline/user.txt` so you get root permission and run `:!/bin/bash` in Vi to gain a root shell	

Reference for exploit : 

https://medium.com/@pettyhacks/linux-privilege-escalation-via-vi-36c00fcd4f5e
https://www.whitesourcesoftware.com/resources/blog/new-vulnerability-in-sudo-cve-2019-14287/

<br>

So i gain a root shell and cat out the root flag

![[root.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/root.png)

![[Pic/Year_Of_The_Rabbit/root flag.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Pic/Year_Of_The_Rabbit/root%20flag.png)

_______________________________________________________________________________________________________________________________________________________________________

That's it for this room quite fun, and i like how the room keep pivoting but for the last part, the creator should have given a little hint to clarify the exploit. That's it for me. Fluffy Signout~~ ʕ•́ᴥ•̀ʔっ
