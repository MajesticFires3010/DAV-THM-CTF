# DAV THM CTF Walkthrough

Solving DAV TryHackMe Capture The Flag [DAV TryHackMe Room](https://tryhackme.com/r/room/bsidesgtdav)

## First Step: Reconnaissance

```sh
nmap -sV -sC -Pn -A 10.10.210.35
```
![Nmap Result](https://github.com/MajesticFires3010/DAV-THM-CTF/assets/96762636/2427edbb-563b-4cd8-99c2-6d64c95ea690)

Here we see only port 80 (http) is open, running Apache httpd 2.4.18 on Ubuntu.
![Apache Default Page](https://github.com/MajesticFires3010/DAV-THM-CTF/assets/96762636/197f3ad7-4b9f-44d8-b3df-ba2b60c06a5b)

Next step is to visit the webpage where we see the default Apache page, nothing really interesting yet.
![FFUF](https://github.com/MajesticFires3010/DAV-THM-CTF/assets/96762636/adeb4fee-4732-4c6d-9b8d-8aa2531c0f08)

## Directory Brute-Forcing

Using `ffuf` (gobuster alternative):
```sh
ffuf -u http://10.10.210.35/FUZZ -e .php,.txt -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-small-words.txt
```

We found `/webdav`, which is interesting. Using BurpSuite or Hydra is not required for this login. A quick Google search reveals that WebDAV stands for "Web-based Distributed Authoring and Versioning," which means it can be exploited. Default credentials for WebDAV in this CTF Room are used: `wampp:xampp`.

Successful login. <br>
![WebDAV Login](https://github.com/MajesticFires3010/DAV-THM-CTF/assets/96762636/c0e67887-cccd-4db2-a614-bf116ff0d1c4)

We get `password.dav` which contains a hash that I couldn't crack. <br>
![Password.dav](https://github.com/MajesticFires3010/DAV-THM-CTF/assets/96762636/1106674c-9581-4ecc-ba54-f97f1bd0805e)

## Exploiting WebDAV

Instead, I searched for other vulnerabilities and found that we can use `cadaver` to upload a reverse shell PHP file.

```sh
cadaver http://10.10.210.35:80/webdav/
put reverseShell.php
```
![Upload with Cadaver](https://github.com/MajesticFires3010/DAV-THM-CTF/assets/96762636/eb3a34ab-6bcd-41e6-88f7-eef1c5f7a24d)

Get the reverse shell PHP from [pentestmonkey](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php). Just modify your attacker IP and port.

Open netcat listener. <br>
![Netcat Listener](https://github.com/MajesticFires3010/DAV-THM-CTF/assets/96762636/155efbc5-7496-4ec4-9940-6cfc44639e27)

Go back to [WebDAV](http://10.10.210.35/webdav/) and run the PHP file we just uploaded. <br>
![Run PHP File](https://github.com/MajesticFires3010/DAV-THM-CTF/assets/96762636/291de06a-c700-4747-ae26-240d5e1fe4ad)

We got the reverse shell.

## Converting to TTY Shell

(Optional but recommended)
```sh
python3 -c 'import pty;pty.spawn("/bin/bash")'
```
![TTY Shell](https://github.com/MajesticFires3010/DAV-THM-CTF/assets/96762636/4753b584-ad5d-416c-8a60-9627d8b51f34)

## Finding Flags

To find `user.txt`, use:
```sh
find / -type f -name user.txt 2>>/dev/null
```
![Find user.txt](https://github.com/MajesticFires3010/DAV-THM-CTF/assets/96762636/cf6830eb-6442-4710-8ced-a47e1b0fd85e)

Then use `cat` to read the flag.

To check for root permissions:
```sh
sudo -l
```
![sudo -l](https://github.com/MajesticFires3010/DAV-THM-CTF/assets/96762636/3c1cea4f-e748-4dc7-9e08-b9204e777cda)

We can run the `cat` command as sudo:
```sh
sudo cat /root/root.txt
```
![Root Flag](https://github.com/MajesticFires3010/DAV-THM-CTF/assets/96762636/dcb6d7bb-efba-41e8-b8a3-9cb1fc3c8239)
<br>

## Congrats!
## We have solved DAV THM CTF!
## Follow My GitHub for more.

