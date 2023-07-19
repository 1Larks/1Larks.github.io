---
author: Larks
title: Overthewire - Leviathan
date: 2023-07-19T09:21:11+03:00
description: My writeups for the Leviathan wargame
math: true
---
> Hey, all! This is a writeup to a overthewire wargame I solved recently.

> This, of course, is a general outline without detailed explanations.

This is the full writeup for the Leviathan wargame, the level is mentioned in the beggining of every level writeup.


> Level 0-

The first level of Leviathan was intuitive and simple, After logging in I used ls -la to see what can I use, Then I entered the only folder that was there called .backup, In the backup folder there was a file called bookmarks.html which contained a huge list of links ,And after opening the file I used grep, so I searched for the word "password" and found the password for the next level.


> Level 1-

Level 1 was a step up from level 0 (surprisingly), There was a SETUID in the main folder, After using it, It required a password, I tried using the previous level's pass for the setuid, And i was absolutley devasted to see it didn't work, So i used the file command to see further information about the setuid, Then I saw it was an unstripped ELF file, So i used ```ltrace``` to check for the password, Then it reavealed the wierdest combination of 3 letters (I have never heard of it and after a google search I read it was some kind of a wierd ritual) Which was "sex", After opening the setuid and entering the strange password I got access to leviathan2's shell, Then I opened /etc/leviathan_pass/levithan2 where the passwords are stored and got the password for the next level.


> Level 2-

This level was great, I had lots of fun completing it, I started with a setuid called "printfile", I was required to enter a path to use the setuid, And the setuid did- well, printed the program, Obviously I tried just entering /etc/leviathan_pass/leviathan3, But it wasn't that easy, The program printed "You can't have this file", So i used ```ltrace``` to check what did the program do, I used ```ltrace ./printfile /etc/leviathan_pass/leviathan3```, And that is what I saw: 
	
	```
    "__libc_start_main(0x80491e6, 2, 0xffffd5d4, 0 <unfinished ...>
	access("/etc/leviathan_pass/leviathan3", 4)                                      
	puts("You cant have that file..."You cant have that file...
	)                                                                          
	+++ exited (status 1) +++"
    ```
After further checking, I realised that it basically checked if I (leviathan2) also had permissions to read the file, That was very confusing at the beggining, After creating a file named "foo.txt" in the folder /tmp/ori that I created, I tried running the setuid with the file using ltrace again, Which resulted in:
	
	```
    "__libc_start_main(0x80491e6, 2, 0xffffd5e4, 0 <unfinished ...>
	access("/tmp/ori2/foo.txt", 4)                                                                                   = 0
	snprintf("/bin/cat /tmp/ori2/foo.txt", 511, "/bin/cat %s", "/tmp/ori2/foo.txt")                                  = 26
	geteuid()                                                                                                        = 12002
	geteuid()                                                                                                        = 12002
	setreuid(12002, 12002)                                                                                           = 0
	system("/bin/cat /tmp/ori2/foo.txt"hi
	 <no return ...>
	--- SIGCHLD (Child exited) ---
	<... system resumed> )
	+++ exited (status 0) +++"
    ```

After reading and researching for a little bit, I noticed that the program actually prints the file only AFTER checking my permissions, But than the hard part  had begun- How the hell could this help me?
After banging my head on the desk for too long, I saw that when you input a file with spaces in the name, Something intresting happens, in the system() call at the end of the program, It call ```/bin/cat``` (cat command) and because of that it actually ignores the "" that is required to cat a file with spaces in the name properly, The file with spaces in the name that I used was called "spaces file.txt" and when I ran ```ltrace``` with the program now, it actually checked the permissions for /tmp/ori/"spaces file.txt", and then continued, Then when calling the cat command, It tried to cat "spaces", Which obviously resulted in an error (Just for now!), After reading more, I noticed something called "symbolic link", Which at the end of the day was the correct thing to use to finish this level, So after some help from wikipedia, I managed to create a symbolic link between /tmp/ori/spaces to /etc/leviathan_pass/leviathan3, That was it!, After running printfile with /tmp/ori/"spaces file.txt", It worked and I got the password for the next level.


> Level 3-

I didn't really understand leviathan 3, It was just like leviathan 1, I got an unstripped setuid that required a password, Then i used ltrace to get further information, While expecting something more challanging and surprising to come, I was pretty bummed out to find that i just got the password for the setuid so quickly, Then I got access to leviathan4's shell and got the password for leviathan4.


> Level 4-

Leviathan 4 was very dissapointing as well, I understand that it's supposed to be very easy, But yet, Leviathan2 was amazing and I have a gut feeling that it was the high point of Leviathan, Anyway, I had a folder called .trash in the main folder, In .trash there was a setuid called bin, after checking some info I ran it and it outputted a word in binary, I translated the code very easily to ASCII, And it was the password for the next level.


> Level 5-

Leviathan 5 was cute, I got another unstripped setuid and ran it, And when I ran it it searched for a file called file.log in /tmp/, So I created the file and ran it to check that everything was working properly, Then I saw the setuid it just ran the file as leviathan6, So I created a symbolic link to 
/etc/leviathan_pass/leviathan6 and everything worked as expected.


> Level 6-

This level was a step up from the last 3 levels, At the beggining I thought it's gonna be like the others, I started with a setuid as usual, The setuid was called leviathan6 and it required a 4 digits pin as an argument, After using ltrace, I saw nothing usefull, I wanted to try doing a simple brute force, So I created a folder at /tmp/ and created a bash script called script.sh, I used vim as my editor, This is the script I wrote for this level:

```
#! /bin/bash

cd ~
for i in {0..9999}; do
    echo at $i
    ./leviathan6 $i
done | grep -v "Wrong"
```

This script worked and I got the pin, Then I got access to leviathan7's shell and I got the password for the next level from /etc/leviathan_pass/leviathan7.
