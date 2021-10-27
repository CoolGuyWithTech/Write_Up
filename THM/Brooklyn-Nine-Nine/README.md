# Brooklyn99

Hello, Welcome Back Today I'm doing a write-up for the tryhackme room *Brooklyn Nine Nine* . This room is relatively easy and their are 2 way of solving it
so no pressure (っ＾▿＾)۶🍸🌟🍺٩(˘◡˘ ) have a drink

-------------------------------------------------------------------------------------------------------------------------------

First we do a normal nmap scan to check what services are up. (I try using threader3000 but it doesn't work for this room)


![nmap](https://user-images.githubusercontent.com/91182217/136354729-10556341-19c7-4f2b-bdf4-84e7824dd627.PNG)

So we that FTP, SSH and HTTP is open
<br>

I first check the website and check the source page and found out this 

![website](https://user-images.githubusercontent.com/91182217/136354722-20b7b97b-d7a3-4020-8dff-970ab408e760.PNG)

It mention stenography for the picture *brooklyn99.jpg*

This is the other way of solving the room, so let's skip it for now

-------------------------------------------------------------------------------------------------------------------------------
**Jake Route**

<br>
Next, we login to the ftp server with anonymous login and we found a *Note_to_Jake.txt* so after using to *get* command to download the .txt 


![note to jake](https://user-images.githubusercontent.com/91182217/136354718-d607a416-4e28-45b8-a9ed-f0d7c6955553.PNG)

we found out that jake have a weak password, and possible username jake,holt and amy

So i try to use medusa to cracks jake ssh password

![medusa](https://user-images.githubusercontent.com/91182217/136354714-1f105ddb-8bad-4d86-8a2c-4a79e216762d.PNG)

And found the password

![jake password](https://user-images.githubusercontent.com/91182217/136354709-701fbd10-7a58-44f4-9a7d-5bac9e76a1d3.PNG)

The password is `987654321`, Classic Jake
<br>

So i use pwncat to ssh into jake account 

![pwncat](https://user-images.githubusercontent.com/91182217/136354707-1a80c819-b32a-4951-948f-aaf088020f04.PNG)

There is nothing in jake directory so i try to access holt and amy directory and in holt directory we found the user flag and a nano.save

![user flag](https://user-images.githubusercontent.com/91182217/136354704-be96dc28-5750-49a4-a90a-dd3eece745c9.PNG)

<br>
I try to cat the nano.save file but i don't have permission, so i thought using linpeas to see what i can use to PE but i then i remember to do some manually enumeration first

So i use sudo -l and found out that we can use less with sudo 

![less](https://user-images.githubusercontent.com/91182217/136354702-ef12eab9-7dcd-469f-a158-2422e37f3571.PNG)

So we go to gtfobins and see what we can find

![gtfo](https://user-images.githubusercontent.com/91182217/136354695-d0ee5794-2102-4cf2-abf2-af0b3a2fb099.PNG)

With that we got root and the root flag

![root flag](https://user-images.githubusercontent.com/91182217/136354699-22af98f0-102d-4dbc-8381-6177ebbfe77f.PNG)

-------------------------------------------------------------------------------------------------------------------------------
**Holt Route**

Hope you haven't finish your drink, cause there is another way to solve this room  (•◡•) /
<br>
So remember the *brooklyn99.jpg* ? we try to use exiftool, binwalk and steghide to see if there is anything hiding

Exiftool and binwalk return nothing to interesting

![exiftool](https://user-images.githubusercontent.com/91182217/136354693-e3f73708-28c2-41d5-a29d-e8d5fce24238.PNG)

![binwalk](https://user-images.githubusercontent.com/91182217/136354691-35e95e79-7d55-4714-99a5-34fb8fbc6730.PNG)

But on steghide we found extra info in the file but it needs a passphrase

![steghide](https://user-images.githubusercontent.com/91182217/136354688-de2ff574-4dcb-40d6-a141-181b4caee13b.PNG)

So we use the tool stegcracker
https://github.com/Paradoxis/StegCracker

To crack the passphrase of the file

![stegcracker](https://user-images.githubusercontent.com/91182217/136354686-21b7227d-3c2e-49b5-8378-70e578152a6f.PNG)

We found out the password is `admin`

and this reveal holt ssh password

![holt password](https://user-images.githubusercontent.com/91182217/136354680-a8ee645d-f624-46f9-9e78-c555ab79f1e2.PNG)

lol

Next, i try sudo -l and found that holt can run nano

![nano](https://user-images.githubusercontent.com/91182217/136354677-97fd3242-a14c-4cb7-9757-6770984287e1.PNG)

<br>
There are 2 ways to get root flag with nano

First Way :

`sudo /bin/nano /root/root.txt` and view the flag

![nano flag](https://user-images.githubusercontent.com/91182217/136354674-d9a3bbdf-595f-4394-9892-2eb36c5f760e.PNG)

Second Way :

So we go gtfobin

![gtfo nano](https://user-images.githubusercontent.com/91182217/136354675-90a0b7a7-897d-43f4-9e0d-760a702d5a56.PNG)

I try so many times and it wouldn't work but in the end, i look how other people did it  

| Steps | Explanation                                                 |
| ----- | ----------------------------------------------------------- |
| 1     | type sudo nano                                              |
| 2     | press Ctrl+R                                                |
| 3     | press Ctrl+X                                                |
| 4     | type `reset; sh 1>&0 2>&0`                                  |
| 5     | press Enter                                                 |
| 6     | You will see a # and the screen will be a bit funny looking | 
| 7     | type clear and enter                                        |
| 8     | Boom!!! Root                                                |

Funny Screen : 

![sudo nano](https://user-images.githubusercontent.com/91182217/136354673-b7da4163-e79f-4293-8d97-ead17b985947.PNG)

Type Clear and press Enter

![clear](https://user-images.githubusercontent.com/91182217/136354668-406a8948-b20f-4d46-ae1a-7e1137f07ec9.PNG)



-------------------------------------------------------------------------------------------------------------------------------
So that's it for this Room its easy and fun hope you guys enjoy it as much as i do. Fluffy Signout~~ ʕ•́ᴥ•̀ʔっ

#Brooklyn99
