Hello~~ this is a write up for the tryhackme room internal


<h2>Enumeration</h2>
<br>
First as usual i run threader3000 to see what ports is open


![threader3000](https://user-images.githubusercontent.com/91182217/135819445-1422e111-94ba-4999-b325-ed0497079d09.PNG)

It shows that port 22 and 80 is open



<br>

So next i try to enumerate more with Nmap

![nmap](https://user-images.githubusercontent.com/91182217/135819428-5e2f2d5a-5138-4e64-bd12-411d946e1ee6.PNG)

And found out that SSH and a web server is present


<br>

I use feroxbuster to see what if there is any hidden directory

![feroxbuster](https://user-images.githubusercontent.com/91182217/135819404-26f6f5b3-8da2-4bbc-a8d7-2c456cc12c8c.PNG)

So a wordpress exist and we have also a login page

<br>
We can use a tool call wpscan to see if their are any vulnerability that the wordpress has

![wpscan](https://user-images.githubusercontent.com/91182217/135819467-c644e45d-15a9-43e4-8d0a-9608f5366695.PNG)

But there is nothing interesting but before that when we visit the page its not loading fully


<h2>Host Change</h2>
<br>

![not loading](https://user-images.githubusercontent.com/91182217/135819431-2e4175a1-987b-4407-bdc5-52ded232cace.PNG)
<br>
and we also found out the host name is`internal.thm` when we visit the login page

![host name](https://user-images.githubusercontent.com/91182217/135819413-95568ec9-21ae-4142-9188-8489a548af0c.PNG)

So we need to change the `/etc/hosts` in the system, i'm using Kali linux for my projects so i just nano the hosts file

![host change](https://user-images.githubusercontent.com/91182217/135819409-eb72dc32-3948-4129-acf7-4cda1e029e62.PNG)

With this change we can access the full website and go to the login page


<h2>Brute Forcing Wordpress</h2>

<br>

On the wpscan we found there is a readme page and we found out that the default username are admin, so we can try to bruteforce it 

I use 2 way to bruteforce it. Using ZAP and wpscan
<br>
ZAP : 
![ZAP brute force WP](https://user-images.githubusercontent.com/91182217/135819399-4f8ae5da-7954-4d38-80f6-314f06f0a642.PNG)
<br>
wpscan :
![wpscan brute force command](https://user-images.githubusercontent.com/91182217/135819463-a66c1bab-11a3-4160-98a5-968c6108a4de.PNG)
![wpscan brute force](https://user-images.githubusercontent.com/91182217/135819465-2509d0bc-c0f9-4a2e-9836-1cf69c1bf5f8.PNG)

Both ways work for brute forcing wordpress credential, i think that in this case wpscan was faster then ZAP but if it gets the job done does it matter?

We now know that the password for admin is `my2boys`




<h2>Wordpress Reverse Shell Exploitation</h2>
<br>
Next we are in the admin panel

![wp admin panel](https://user-images.githubusercontent.com/91182217/135819456-a3b377d1-f5f8-432e-80b6-bc1e7b0ca275.PNG)

<br>

I try to create a reverse shell with wordpress using the theme editor in wordpress, i follow a tutorial for this.
https://www.hackingarticles.in/wordpress-reverse-shell/

![wp theme editor](https://user-images.githubusercontent.com/91182217/135819461-1bd5e06d-5b80-420c-847e-1047e5abc69a.PNG)
<br>

I got a connection!!

![pwncat 9999 from wordpress](https://user-images.githubusercontent.com/91182217/135819434-62efa89f-9c95-4601-bc8d-a43330369bd5.PNG)


<h2>Machine Enumeration</h2>
<br>

After getting into the system i try some manually enumeration and found nothing, so i use linpeas

![linpeas](https://user-images.githubusercontent.com/91182217/135819426-320e97b8-980c-4e57-9eec-40fcb5eb4fde.PNG)
<br>

But linpeas also found nothing too interesting, next i check out the opt directory and found a user login credential

![user password](https://user-images.githubusercontent.com/91182217/135819452-8e61f55b-1bbc-429d-a53d-555d4507eb00.PNG)
<br>

So i su to the user and found the user flag

![user flag](https://user-images.githubusercontent.com/91182217/135819449-1730b88c-6e40-4ad4-bec9-a7729ba180c7.PNG)


<h2>Root Access</h2>
<br>
I cat out the files that is in the user directory and found out that there was a internal server running

![internal server running](https://user-images.githubusercontent.com/91182217/135819420-0609f318-2da8-4f22-8e99-f0b0c9e50e0f.PNG)
<br>

So i use SSH -L to route the traffic

![route services](https://user-images.githubusercontent.com/91182217/135819442-af80e9e7-13b3-463e-817a-821615fe12b4.PNG)

And visit the webpage and found a login in panel 

![internal server page](https://user-images.githubusercontent.com/91182217/135819418-079b0b14-d33f-43d9-b709-9e4330620f72.PNG)
<br>
So i try using ZAP again to brute force the login using the username admin

![ZAP brute force Jenkis](https://user-images.githubusercontent.com/91182217/135819468-c7cfde4d-df67-4d48-a40d-a164bb735f89.PNG)

found the password `spongebob`

<br>
After logging in the jenkins i go to the script console to do a reverse shell (I have previous knowledge about jenkins)

![jenkis console](https://user-images.githubusercontent.com/91182217/135819421-930c94b7-f4bf-4631-9384-b8d8bb2b3039.PNG)

<br>

With the help of the guide i successfully have a shell

https://blog.pentesteracademy.com/abusing-jenkins-groovy-script-console-to-get-shell-98b951fa64a6

![reverse shell jenkis](https://user-images.githubusercontent.com/91182217/135819437-bb8eb609-9a09-4eac-99ef-67cf12201ba6.PNG)

<br>

Next i check out the opt directory and found a note.txt and inside the txt file it has the root password

![jenkis opt root password](https://user-images.githubusercontent.com/91182217/135819423-60a89c7c-d714-4843-b981-c7cfc848b733.PNG)

<br>

So i su to root and cat out the root flag

![root flag](https://user-images.githubusercontent.com/91182217/135819439-41810901-500d-43ca-89b9-ba352927ddad.PNG)

---------------------------------------------------------------------------------------------------------------------------------
That's it that's the end of the room hope you enjoy it. If there is anything that i can improve on feel free to contact me. Fluffy Signout~~ ʕ•́ᴥ•̀ʔっ
<br>
Things i learn from the room
-	wpscan
-	SSH -L routing

---------------------------------------------------------------------------------------------------------------------------------

