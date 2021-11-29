Hello Everyone, this is a walkthrough for Mercy Box on Vulnhub. Hope you enjoy 

-------

## Discovery/ Recon 

First i did a ping scan from nmap and find the IP address of the machine

Next, i run my script for nmap 


![](https://i.imgur.com/qIOOWVj.png)


We found that a-lot of ports that is open, but port `8080, 139, 445` which is  a http page and a smb Share


<br>

### Web Recon

So i visit the 8080 page on found this Page

![](https://i.imgur.com/wn57KuA.png)


The page links to a manager app panel which need credentials to login 

So i run feroxbuster to see what we found


![](https://i.imgur.com/6BiwTIw.png)


It found `robots.txt` and the directory shows 


![](https://i.imgur.com/O54EhRL.png)]


The pages shows a texts that is encoded in base64


![](https://i.imgur.com/mkj3DtT.png)


So i put that into cyberchef 

![](https://i.imgur.com/trjQ3Cr.png)


from this we know that users are using `easily crackable passwords` and the are also using `password` as the password (Big Brain!!!) 

<br>



### SMB Share

I run enum4linux to enumerate the share


![](https://i.imgur.com/sm1boQg.png)


From the scan we know that a share call `qiu` is available but it needs a password to get in

But we can try `password` as the password from the information we gather from the webpage

So i connected to the Share using the password, and we got in

![](https://i.imgur.com/WHP7eBf.png)


I go into the .private folder and their are 1 files and 2 directory

![](https://i.imgur.com/wgfqIzo.png)


So in the directory we found a config file, so port 22 and 80 needs to be knock


![](https://i.imgur.com/aP27DYX.png)


<br>


## Exploit

After knocking the ports i access the page and run a feroxbuster session on it


![](https://i.imgur.com/iYo9mwN.png)


Which tell us that robots.txt is available and show us 2 additional directory 


![](https://i.imgur.com/n1fr7pR.png)


On the mercy directory just include a index.html file


![](https://i.imgur.com/0YGSStO.png)


On the nonmercy directory it was a framework of some sort, after doing some research it was a static code analyzer framework

So i search on searchsploit to see what is can found, a file inclusion vulnerability 

![](https://i.imgur.com/cRDHZGX.png)


And with the help of feroxbuster we know that this vulnerability exist because the directory is there 


![](https://i.imgur.com/5mjkAmd.png)


We can see /etc/passwd and look for usernames we can brute force and since we know that 8080 is hosting tomcat we can try going to the tomcat config file


![](https://i.imgur.com/8qjeZAi.png)


And we got 2 credential that we can use 

After logging in to the 8080 management panel  with one of the credential, we are in the admin panel

After some googling we can use the admin panel to create a reverse shell that we could connect to [Reverse Shell](https://github.com/mgeeky/tomcatWarDeployer) 


![](https://i.imgur.com/oZtyhed.png)

## Root 

On the home directory the author is trolling us (ㆆ_ㆆ)


![](https://i.imgur.com/Cb7mO3c.png)


So after some manual enumeration, i found nothing and process to use linpeas


![](https://i.imgur.com/UX6qa9S.png)



![](https://i.imgur.com/lr20Et2.png)


I found that we can use dirty cow on the machine, but in the end this was a rabbithole 

So i try the other credential we found on the tomcat config file and try to su to it. 

![](https://i.imgur.com/hrLawMQ.png)


So i look at fluffy (that's me ʕ•́ᴥ•̀ʔっ) file directory and found 3 files 


![](https://i.imgur.com/wtm14NH.png)


The content of the file are backing up files and display the time


![](https://i.imgur.com/kmkKgiP.png)


On port 80 we also found the /time directory which we display the time of the machine

So they are using this script to get the time, which the script we can edit on 

So i just append a reverse shell onto the file

![](https://i.imgur.com/aWzP9wZ.png)


With that we got root access


![](https://i.imgur.com/PkbgtoT.png)


And it was successful, we got the root flag!!!!

![](https://i.imgur.com/X1ymHZq.png)

----

The room was very fun, but i did use alot time on the dirty cow exploit. But all and all the box is very fun. Hope you guys enjoy it. Fluffy Signout~~ ʕ•́ᴥ•̀ʔっ 


