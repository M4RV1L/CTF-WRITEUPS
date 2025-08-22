# PICKLE RICK 🥒

![](screen/img.jpeg)

Let's start with the port enumeration

![](screen/scan.png)

![](screen/scanports.png)

Browse to webpage at the `IP MACHINE`

![](screen/site.png)

Let's analyze the site code

![](screen/controlusite.png)

We find a username, it will be useful later

We use gobuster to find some directories and pages on the site, infact we found many pages

![](screen/gobuster.png)

Browse to `robots.txt` and we found a password

![](screen/robots.png)

Browse to `login.php`, we have a login page. Let's try to use the credentials found before

![](screen/login.png)

![](screen/portal.png)

Let's see what's inside the directory

![](screen/ls.png)

Let's try to read the file `Sup3rS3cretPickl3Ingred.txt`

![](screen/cat.png)

OK, there's not much we can do.
We use a reverse shell.

We use the pentestmonkey cheat sheet for PERL reverse shell.
So start a listener on port 1337

![](screen/nc.png)

Let's copy the command and change the IP and the port

![](screen/reverseshell.png)

![](screen/reverseshellpanel.png)

WELL DONE!

![](screen/inside.png)

So... Let's take the first flag

![](screen/1flag.png)

Let's examine the file `clue.txt`

![](screen/clue.png)

Browse to /home/rick folder and take the second flag

![](screen/2flag.png)

Let's try to access the root folder

![](screen/cdroot.png)

To escalate privileges we try to identify which commands are allowed to the current user with root privileges... all commands are allowed without any password

![](screen/sudo-l.png)

We use `sudo bash -i` to gain root access
Browse to /root folder and take the third flag

![](screen/3flag.png)






















