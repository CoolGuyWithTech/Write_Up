This is a write up for the anonymous room on tryhackme, hope you enjoy

----------------------------------------------------------------------------
First, i use threader3000 to find what ports are open

![threader3000](https://user-images.githubusercontent.com/91182217/135233000-f047ef94-b49e-4930-8910-86fa4594c446.PNG)

Next i use nmap to scan it thoroughly 

![nmap](https://user-images.githubusercontent.com/91182217/135233005-de96728d-4692-4c58-8851-2957d95455af.PNG)

So we found out that SMB and FTP allow anonymous login

This should end the enumeration phase.

----------------------------------------------------------------------------
Now the room ask how many ports are open and the answer is `4`

And port 21 is running `FTP`

And port 139 and 445 is running `SMB`

----------------------------------------------------------------------------

Next i run enum4linux to enumerate the shares in the system

using the command enum4linux -S **IP-Address**

![smb share](https://user-images.githubusercontent.com/91182217/135233952-fe0908ca-52c2-46d2-960f-87259dba711b.PNG)

And found out the is `pics`

----------------------------------------------------------------------------
Now i connect to the FTP server first to see what i can found, using the anonymous login

![ftp files](https://user-images.githubusercontent.com/91182217/135234796-493a233a-d235-48a1-8368-fa6c12f2ea99.PNG)

And i found 3 files, i use *mget* to download all the files  

Remove-Log

![logs](https://user-images.githubusercontent.com/91182217/135235192-43eb2180-4a4c-4df2-8da8-04013873a2e6.PNG)

To-Do

![to-do](https://user-images.githubusercontent.com/91182217/135235195-c2f74fc7-e1a8-4f7d-97e8-582208d36252.PNG)

You should my man
<br>
And then i cat out the clean.sh

![clean](https://user-images.githubusercontent.com/91182217/135235959-4445a242-fd95-438d-9a9a-fe879ee9612c.PNG)

From the output we can figure out that its cleaning out the temporary files in the system and from the remove-log we can estimated that this is a recurring job so its using cronjob. 
<br>

My plan is to create a reverse shell using the .sh file, but i would need write permission in the ftp folder so i try to put files into the directory and it works!!

![put](https://user-images.githubusercontent.com/91182217/135235957-549da88c-7f8c-4688-8893-e6bd51f0b79e.PNG)

<br>

So now added a connection code into the clean.sh file and put it to the ftp directory and replace the current file 
(Google bash reverse shell)
(Since the clean.sh file is using bash so using bash code should work)
![clean-up](https://user-images.githubusercontent.com/91182217/135235954-35bdc495-afe9-4f40-ae4e-a36a63751318.PNG)
<br>
And now i open up pwncat cat and listen on the connection

![pwncat](https://user-images.githubusercontent.com/91182217/135235952-b7e33f85-61e3-4532-9819-43d35b9d692b.PNG)

And Bang, pwncat cat receive a connection and we are in.

----------------------------------------------------------------------------
Next, i just cat out the user.txt file 

![user](https://user-images.githubusercontent.com/91182217/135238437-40ba6893-3bcb-4c82-82f8-2424ac46d3ab.png)

And this is the user flag

![cat user](https://user-images.githubusercontent.com/91182217/135238916-e1acc128-85e5-49fb-b967-21ca745e4b39.PNG)

----------------------------------------------------------------------------
After getting the system i did some manually enumeration 

![id](https://user-images.githubusercontent.com/91182217/135238430-25c958d0-846a-48d8-9063-d100ff244113.PNG)
![sudo](https://user-images.githubusercontent.com/91182217/135238432-ae461ca8-051f-4349-97c1-76f9183ed4d7.PNG)

And find out that we have sudo id but we don't have the password for namelessone, so decided to upload linpeas and run it

![linpeas](https://user-images.githubusercontent.com/91182217/135238420-45535467-3845-49c8-8866-d91315ab7073.PNG)
<br>
On linpeas i found out that env has a SUID set

![env](https://user-images.githubusercontent.com/91182217/135239337-9f028be8-105e-42bf-9791-6b85ef193e22.PNG)

So i search it on gtfobin

![gtfobin](https://user-images.githubusercontent.com/91182217/135239346-b245ff60-7292-4dbe-a3a6-56c29eaf6c3c.PNG)

I ran the command

![env root](https://user-images.githubusercontent.com/91182217/135239342-5e9ce7d8-de45-409c-8ff3-36c1f1a4cb1b.PNG)

There we go, we got root access


![root](https://user-images.githubusercontent.com/91182217/135239785-3c34ffac-3d74-40d6-aebe-7787c4c3464a.PNG)

Here is the root txt

----------------------------------------------------------------------------
### Additional Info 

some people go with the SMB share first and see they can find, and i was lucky that the SMB shares are just a dead end, but the order doesn't matter its good practice to see what we can find with the resources that we had.
<br>
On linpeas we also can see that we have lxd id which means that we are in a container so we could this to PE as well. 
<br>
Here is a write-up and a link for lxd PE 

https://reboare.github.io/lxd/lxd-escape.html

https://musyokaian.medium.com/anonymous-tryhackme-c15478d23623

Thanks for reading until this point, hope you enjoy the write up and if there is any comment feel free to talk about it.  Fluffy Signout~~ ʕ•́ᴥ•̀ʔっ
