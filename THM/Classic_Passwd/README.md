Hello, today imma gonna try to solve the room Classic Passwd on tryhackme. Hope you enjoy.

------------------

Let's try the most basic method, strings

![[strings .png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Attachment/Classic%20Passwd/strings%20.png)

and as expected it return nothing that indicate anything out of the ordinary 

<br>


There are a couple of ways to solve this room, and i'm going to try to list all the methods that i try and work


<br>


## ltrace

You could use ltrace on the program, and it would tell you what it's comparing to


![[ltrace.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Attachment/Classic%20Passwd/ltrace.png)

The program is trying to compare our input with `AGB6js5d9dkG7` , so after input the string we got the flag

![[flag.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Attachment/Classic%20Passwd/flag.png)

It's that easy


## Ghidra 

Open the program in ghidra and look at the code

We first go into the main function and see what is being call

![[main fuction.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Attachment/Classic%20Passwd/main%20fuction.png)

We have a vuln and gfl function

Here we have 2 ways of to get the flag, but analyzing the function code

<br>

### Vuln 

In the vuln function it have a couple of variables and it compare's the input with the variable in the program

![[authentication code.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Attachment/Classic%20Passwd/authentication%20code.png)

So what i did is converting the hex to ascii on cyberchef, and i got the creator github page and the string it compare with

The reason im reversing the order of the hex because we are using little indian as the format, so we need to reverse the order to get the actual text

![[hex github.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Attachment/Classic%20Passwd/hex%20github.png)

![[hex text.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Attachment/Classic%20Passwd/hex%20text.png)

And we input the text into the program and we will get the flag

![[flag 1.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Attachment/Classic%20Passwd/flag%201.png)


<br>



### gfl

When we check the gfl function, the flag is hard coded into the program, so we just have to convert the hex.


![[easy way.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Attachment/Classic%20Passwd/easy%20way.png)

I try to convert the hex into ascii code, but it was nonsense

![[gfl ascii.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Attachment/Classic%20Passwd/gfl%20ascii.png)

So i try to convert it into decimal and submit the flag

![[hex to decimal.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Attachment/Classic%20Passwd/hex%20to%20decimal.png)

![[hex to decimal 2.png]](https://github.com/CoolGuyWithTech/Cybersecurity/blob/main/Attachment/Classic%20Passwd/hex%20to%20decimal%202.png)

The flag `THM{65235128496}`

and it work!!


---------------
That's it for this room, i hope it helps you and if there is anything i could improve it, please leave a message Fluffy Signout~~ ʕ•́ᴥ•̀ʔっ

