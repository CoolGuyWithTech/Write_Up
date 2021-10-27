Hello, this is my first write-up. I'm still learning as it stands so if there is any recommendation or things that can be improve please tell me. Thanks (ɔ◔‿◔)ɔ ♥


-------------------------------------------------------------------------

After Deploying the machine, I run an nmap scan 

![nmap](https://user-images.githubusercontent.com/91182217/134301851-08373540-cbff-4087-bca4-76fff048dcf1.PNG)

And found out that HTTP was open so I enter (It's quite obvious since the room is CMS Related)


![homepafe](https://user-images.githubusercontent.com/91182217/134302279-5df59b08-98b6-4f0b-be0d-a0310678e7ff.PNG)

On the home page, we found out that spider-man has robbed a bank. Welp superhero still need money to survive I guess

So the answer is `spiderman `

-------------------------------------------------------------------------
Next, The Task ask us about the version of the CMS

So I decided to run feroxbuster (similar to gobuster) and see what I can find 

![feroxbuster](https://user-images.githubusercontent.com/91182217/134302891-02a62d3c-0e41-43a5-b672-64343bfd8470.PNG)

I found Robots.txt and a README.txt

In the README.txt it said that the current version is 3.7.x

![readme](https://user-images.githubusercontent.com/91182217/134303367-41a73eed-b158-4738-bc45-d1440441680b.PNG)

And In the txt, it also gives us a version list which 3.7 has, so I try 3.7.0, 3.7.1 and etc

So the answer is `3.7.0`

-------------------------------------------------------------------------

Next, it asks us to find the crack password of the user so naturally, I go searchsploit and find a sqlmap script

![sqlmap](https://user-images.githubusercontent.com/91182217/134303905-ed4a01ad-90c0-4add-91c6-e27495362f62.PNG)

But I found out that sqlmap was too slow and the room creator also hint at us that we can try using python

So after a google search, I found this script

https://github.com/stefanlucas/Exploit-Joomla

Which is Way faster and it dumps information for the username and password

![python](https://user-images.githubusercontent.com/91182217/134304368-82c300ed-cdc0-4dbd-a26b-62e3eae5480a.PNG)

We found out the username is jonah and the password hash

Next, we will use John to crack the hash (You could use Hashcat if you have a good graphic card, I use john due to I'm using a VM)

![john](https://user-images.githubusercontent.com/91182217/134304374-3ce90933-a3f5-4734-a279-4757009eedcc.PNG)

We found out the password is `spiderman123`(A spiderman fan it seems)

-------------------------------------------------------------------------

Next, I log into the account and from what we found in feroxbuster and in the robots.txt file there is /administrator directory

I was on the page, I go around and see what I can found

Then I google Joomla reverse shell and I follow instructions on how to deploy a reverse shell using the template section

https://www.hackingarticles.in/joomla-reverse-shell/

setting up a netcat session with it, I try using pwncat but it took too long for me

and press the template preview button

![reverse shell](https://user-images.githubusercontent.com/91182217/134305480-2ad36b08-c16b-40fa-991e-181dd7ae85d7.PNG)

And your ncat should be in

![ncat](https://user-images.githubusercontent.com/91182217/134305774-51bc5f76-6bfe-4b02-b395-5e878a79367f.PNG)

Next, I try to use ncat on the machine and call back to my machine which pwncat was listening

![ncat pwncat](https://user-images.githubusercontent.com/91182217/134305786-197b8ed6-c6fb-4a3c-81be-0ea2df0a7712.PNG)

And then boom pwncat session

![pwncat](https://user-images.githubusercontent.com/91182217/134305782-a9c1b838-5af9-44c5-94bc-c1419995429e.PNG)

Next, I run linpeas on the machine which I store in /dev/shm (Thanks to John Hammond) and execute it

![password](https://user-images.githubusercontent.com/91182217/134306354-345dd72e-c5a9-4549-b968-b335623a8986.PNG)

Linpeas found a password in the config PHP file which is located in /var/www/html

And we know that is another user exist with linpeas and some manual enumeration 

So I try to su to jjameson and it works! and I can cat the user.txt in the user directory

![user](https://user-images.githubusercontent.com/91182217/134306740-4aeb3276-5670-4bf2-b57d-48812e74f901.png)

-------------------------------------------------------------------------

Now we can try sudo -l and see what we can do, and found out that we can run yum with sudo 

![yum](https://user-images.githubusercontent.com/91182217/134306977-d6bd7ee5-b6f8-427c-be81-e28ab26ce227.PNG)

So with a little google with gtfobin can find us a PE method

Remember to include the [main] I originally thought that it's just there for separation, but I was wrong (ㆆ_ㆆ)

![yum exploit](https://user-images.githubusercontent.com/91182217/134306976-64c6a175-6d4b-42f7-9b13-06006990fdf1.PNG)

After running it, and bam we got root so cd to /root and cat the root.txt

![root](https://user-images.githubusercontent.com/91182217/134306972-2e3806ec-6e3c-4ee4-b77e-e31b3d140323.PNG)

And you are done!!!

The room was nice and interesting, the password part took me some time cause I thought it was a hash or something but in the end, I found out that it was the actual password. Good luck hacking and have fun otw.

Fluffy Signout~~ ʕ•́ᴥ•̀ʔっ
