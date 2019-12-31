# Building a LAMP stack using the Raspberry Pi.

This tutorial was written by [Lindsay K. Mattock](http://lindsaymattock.net) and adapted by [Katherine Walden](https://github.com/kwaldenphd). 

<a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license"><img style="border-width: 0;" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" alt="Creative Commons License" /></a>
This tutorial is licensed under a <a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license">Creative Commons Attribution-NonCommercial 4.0 International License</a>.

Wikipedia's ["LAMP (software bundle) article"](https://en.wikipedia.org/wiki/LAMP_(software_bundle)) includes the following description:

"LAMP (Linux, Apache, MySQL, PHP/Perl/Python) is an archetypal model of web service stacks, named as an acronym of the names of its original four open-source components: the Linux operating system, the Apache HTTP Server, the MySQL relational database management system (RDBMS), and the PHP programming language. The LAMP components are largely interchangeable and not limited to the original selection. As a solution stack, LAMP is suitable for building dynamic web sites and web applications."

## Lab Objectives:
By the end of this project you will be able to:
-	Work at command line to configure the Firewall
-	Automate the updates and upgrades on your Pi
-	Install and configure Apache, MySQL, and PHP on your webserver
-	Explain the function of a web server in delivering content to the WWW

## Acknowledgements

This lab is based on the "Project 5: LAMP" project materials developed by [Lindsay K. Mattock](http://lindsaymattock.net/) for the the [SLIS 5020 Computing Foundations course](http://lindsaymattock.net/computingfoundations.html). 

Dr. Mattock's materials are adapted from Jonathan Martin’s [Creating LAMP Infrastructure for DH Projects](https://dhlinux.org/wiki/index.php?title=Main_Page) DHSI 2016 course.

# Table of Contents
- [Web Browser for this Lab](#web-browser-for-this-lab)
- [Working at the Command Line](#working-at-the-command-line)
- [Task 1: Setting Up the Firewall](#task-1-setting-up-the-firewall)
  * [Uncomplicated Firewall (UFW)](#uncomplicated-firewall-ufw)
- [Task 2: Automating Your Upgrades and Updates](#task-2-automating-your-upgrades-and-updates)
- [Installing the LAMP Stack](#installing-the-lamp-stack)
  * [Task 3: Installing Lynx](#task-3-installing-lynx)
  * [Task 4: Installing Apache](#task-4-installing-apache)
  * [Task 5: Setting up a Virtual Host](#task-5-setting-up-a-virtual-host)
  * [Task 6: Installing PHP](#task-6-installing-php)
  * [Task 7: Installing MySQL](#task-7-installing-mysql)
- [Endnotes](#endnotes)
- [Lab Notebook Questions](#lab-notebook-questions)

## Web Browser for this Lab

Be sure to use the Chromium web browser installed on you Pi when loading web pages in a GUI browser during this lab.

# Working at the Command Line

We worked a bit at the command line during the end of the OS installation and Pi configuration. For this next project we will work almost exclusively at the command line. Remember that all of the functions that you see at the GUI (Graphical User Interface) can be performed at the Command Line. 

The GUI has limitations – you can only perform the functions that have been programmed into this interface. The Command Line is the pre-cursor to the windows-style GUI. While it is a bit less dynamic and not immediately intuitive, you have much more power working at the Command Line. As you perform the various tasks, you will see that every aspect of computing can be reduced to a few commands in text form.

To get started, open up a Terminal session on your Pi. What follows is a step by step guide to building a LAMP server. Follow the steps, write down what you did in your lab notebook, and record your questions and observations. If you get stuck, turn to your rubber duck, the instructor, course mentor, your classmates, or the Internet for help.

<blockquote>Server: A server is simply a computer that is designed to provide a service to another computer (“client”). A file server is dedicated to storing and providing access to files, a print server provides printing for many computers, and a mail server handles email within a network. You engage with servers every day when you make a request to view a particular website. The files for that website are hosted on a server. When you type the URL into the search bar or click on a URL from search engine results, your computer (the client) sends a request to the server to see that information.</blockquote>

# Task 1: Setting Up the Firewall

We need to make sure that our OS is up-to-date. Security releases and updates are released frequently, so it is good practice to update your OS on a regular basis. You have probably been prompted frequently with these updates on your PCs, Macs, and mobile devices. 

Our Linux-based OS doesn’t come built in with these reminders, so we have to run this set of commands by ourselves. We will automate this is a bit. Run `sudo apt-get update && sudo apt-get upgrade`.

<blockquote><code>&&</code> is the <code>AND</code> operator for commands. The second command will be executed if and only if the first is executed and exited successfully. Note that you must type <code>sudo</code> for the second command as well or it will not be executed.</blockquote>

## Uncomplicated Firewall (UFW)

<blockquote>Firewall: The server’s job is to respond to requests from the outside world and provide access to the information on the server. However, we want to make sure that we can control who has access to the server to prevent attacks, viruses, and unauthorized users from gaining access to the server. As you will see, the firewall is just a set of rules that defines how other computers can connect to the server.</blockquote>

1. Run `sudo apt-get install gufw` to install Uncomplicated Firewall (UFW) with the graphical user interface. Type `Y` when prompted.

<blockquote>Additional resources:
  <ul>
    <li><a href="http://www.dedoimedo.com/computers/gufw.html">An Introduction to GUFW</a></li>
    <li><a href="https://www.linux.com/learn/introduction-uncomplicated-firewall-ufw">An Introduction to Uncomplicated Firewall</a></li>
    <li><a href="https://www.digitalocean.com/community/tutorials/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server">Uncomplicated Firewall How To Guide</a></li>
    <li><a href="https://help.ubuntu.com/community/UFW">UFW Help Guide</a></li>
  </ul>
</blockquote>

2. Use `gufw` to open the firewall

This command will open the Graphical User Interface for the Uncomplicated Firewall. You can also configure the firewall at the command line. 

The tutorial includes instructions for both, but you can only work at the GUI or CLI--not both. Pick either the GUI or CLI to follow over the next few steps.

3. Turn the firewall on:
  * GUI: Click the `Unlock` button at the bottom right of the open window; enter your password if prompted; Use the slider to change status to `ON`
  * CLI: `sudo ufw enable`
 
** If you receive an error about the ipv6 tables, run the command `sudo nano /etc/modules`. When the file opens, add the line `ipv6` to the document. Hit <control-X> to exit, <Y> to save. Then reboot your pi with `sudo reboot`.

4. Deny all incoming connections:
  * GUI: From the Incoming dropdown, select `Deny`
  * CLI: `sudo ufw default deny incoming`

5. Allow all outgoing connections:
  * GUI: From the Outgoing dropdown, select `Allow`
  * CLI: `sudo ufw default allow outgoing`

<blockquote>Q1: Explain this set of commands in your own words.</blockquote>

These rules provide protection from incoming attacks, but we do need to allow some two-way communication in order to provide some services on our servers. We can do this by setting some basic rules.

6. Allow access on Port 80:
  * GUI: Click on the `+` in the bottom left corner of the firewall window; in the select the following from the dropdown boxes `Allow In Service HTTP`
  * CLI: `sudo ufw allow www`

<blockquote>Port: Your computer has physical ports and logical ports. Physical ports are where you physically attach an accessory (keyboard, mouse, monitor) to exchange information with the computer. When we talk about ports on the server, we are talking about logical ports. These are designated connecting “places” that are defined by specific Internet Protocols. See Port 80, below as an example.</blockquote>

<blockquote>Protocol: Very simply, protocols are agreed upon rules governing how computers connect to each other and how information is exchanged. A protocol defines how the information is formatted into packets and exchanged between computers. For more information about how HTTP protocols work, see Janet Evans's <a href="https://gumroad.com/l/http-zine">"HTTP: Learn your browser's language" zine</a>.</blockquote>

<blockquote>Port 80: Is the logical port designated for web-based requests or requests to access the webpages on the server. Port 80 uses the Hypertext Transfer Protocol or HTTP. This should look familiar. This is the protocol that governs the way that information is exchanged between computers on the world wide web. For technical details see http://fab.cba.mit.edu/classes/961.04/people/neil/ip.pdf.</blockquote>

<blockquote>Q2: Where do you commonly see HTTP? Why?</blockquote>

7. If you are working at the command line, check the firewall status with `sudo ufw status` to check to see if your rules have been enabled. If you are working at the GUI, close the firewall window. 

8. You can also use `sudo ufw status` to check the firewall status.

You have now established a basic firewall. There are many more rules that we could set to control the exchange of information over your server, you can review these rules in the documentation for UFW.

<blockquote>Q3: Now we have a basic firewall established. Can you explain what the firewall is doing in your own words? Would you set any additional rules? Use the <a href="http://www.pearsonitcertification.com/articles/article.aspx?p=1868080">TCP/IP Ports and Protocols chart</a> to review common ports and services. You may also wish to set additional rules on your firewall using this chart.</blockquote>

# Task 2: Automating Your Upgrades and Updates

Ideally, we would want to update the OS on a regular basis. The easiest way to do this is to automate the updates. So, before we install the LAMP software, let’s automate this process.

1. Use `sudo apt-get install unattended-upgrades` to install the unattended-upgrades software

2. Type `Y` to continue.

3. Use `sudo nano /etc/apt/apt.conf.d/10periodic` to open the `10 periodic` file.

```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "7";
APT::Periodic::Unattended-Upgrade "1";
```
4. This set of commands updates the package lists (the new updates available), downloads, and installs the available upgrades every day (designated with a `1`). The `AutocleanInterval` cleans up your downloads folder once a week (a 7 day interval).

5. `<Ctrl> X` to exit; `Y` to save; `<enter>` to save with the same file name

6. Now we are going to configure which upgrades we would like to install. Open the 50unattended-upgrades file `sudo nano /etc/apt/apt.conf.d/50unattended-upgrades`

7. When you open the file you should see quite a bit of text. Note that here the commented text is preceded by a `//` instead of a `#`. We have seen different forms of commenting throughout the term. Remember that commented text is not read by the computer as a command. This is the explanation of the code that is written there for us.

8. Scroll down to find the uncommented line `Unattended-Upgrade::Origins- Pattern{`. We want to make a few edits here. 

9. Uncomment or add the following lines in this section:
```
"o=Raspbian,n=jessie";
"o=Raspbian,n=jessie,1=Raspbian-Security";
```
10. `<Ctrl> X` to exit; `Y` to save; `<enter>` to save with the same file name
  
11. Now each day the Pi will automatically check for and install any updates to the OS, including security updates.

# Installing the LAMP Stack

What is a LAMP stack? LAMP is very simply a bundle of software that are used together to build dynamic websites and host them on servers. You’ll see later this term that many popular platforms like WordPress require a LAMP stack. LAMP stands for Linux, Apache, My SQL, and PHP/Python/Pearl. We already have the L (Linux) with our Raspbian OS. 

Before we get started, we are going to install a simple web browser that we can use at the command line to check our configuration as we install each piece of software.

## Task 3: Installing Lynx

<blockquote><a href="https://en.wikipedia.org/wiki/Lynx_(web_browser)">Click here</a> to learn more about the Lynx command-line web browser</blockquote>

1. Install Lynx using `sudo apt-get install lynx`

2. With Lynx installed, use the command `lynx` followed by a web address to navigate the web. Try navigating to a few of your favorite websites.

<blockquote>Q4: Describe the differences between viewing websites in Lynx and through Firefox/Chrome/Safari/your favorite web browser.</blockquote>

3. Type `<q>` to quit Lynx.
  
## Task 4: Installing Apache
  
What is Apache? Apache is the Web server. This is the software package that allows your computer to offer services to the web. Apache2, and other web servers, use HTTPD or Hypertext Transfer Protocol daemon. This is the program that sits and waits for HTTP requests from other computers and then responds to these requests.

<blockquote>HTTPD: Remember, HTTP is the (Hypertext Transfer Protocol) or the set of rules that allow for the transfer of files (text, images, sound, etc.) on the World Wide Web. D stands for daemon, defined below.</blockquote>

<blockquote>daemon: a program that runs continuously and exists for the purpose of handling service requests that a computer system expects to receive. The daemon program forwards the requests to other programs (or processes) as appropriate.<sup><a href="#fn1" id="ref1">1</a></sup> In this case Apache is the HTTP daemon. Other daemons handle e-mail and printing requests, for example. See also http://www.linfo.org/daemon.html.</blockquote>

1. Install Apache using `sudo apt-get install apache2 -y`

<blockquote>Q5: What does the <code>–y</code> mean in this command? Use Explain Shell http://explainshell.com to find out.</blockquote>
  
2. Many applications will use Apache’s `Mod_Rewrite` capabilities. This function allows you to create human-readable URLs (i.e. gizmo.com/latest_and_greatest/specific_gadgets/exactly_what_youre_looking_for VS. http://www.gizmo.com/gp/itemB004RYVI0Q/ref=as_li_ss_tl?) More on this later. 

3. Activate this rewrite function using `command sudo a2enmod rewrite`

4. Restart Apache using the requested command. Remember to use `sudo`

5. Take a look at the hosts file using `sudo nano /etc/hosts`

6. The `hosts` file should look something like this:
```
127.0.0.1    localhost
::1          localhost ip6-local host ip6-loopback
ff02::1      ip6-allnodes
ff02::2      ip6-allrouters

127.0.1.1    raspberrypi
```

7. The hosts file maps your computer to the network, that is, it defines the host name (the human readable name of the computer) and the IP address.

8. `localhost` always refers to your local computer. This is your Pi. `raspberrypi` also refers to your computer. Remember that at the command line you are `pi@raspberrypi`, or the user Pi on the computer `raspberrypi`. 

9. The localhost IP address is always `127.0.0.1`. The `loopback` that you see referenced in the file refers to the fact that this IP address will always loop back you your computer. We cannot access each other’s Pi’s using the local host. Instead we would need to define a unique IP address in this file. We won’t worry about this at the moment.

<blockquote>IP address: An identifier for a computer or device on a TCP/IP network. Networks using the TCP/IP protocol route messages based on the IP address of the destination. The format of an IP address is a 32-bit numeric address written as four numbers separated by periods. Each number can be zero to 255.<sup><a href="#fn2" id="ref2">2</a></sup></blockquote>

10. We will make some modifications to this file later. For now, just `<control> X` to exit. `N` to discard any changes if prompted.

11. Now each day the Pi will automatically check for and install any updates to the OS, including security updates.

<blockquote>Q6: The definition above states that the IP address is a 32-bit address, explain what this means in your own words (think back to the binary math project).</blockquote>

<blockquote>ip6 or IPv6: The 32-bit IP addresses are based on the IPv4 or version 4 of the IP protocol.  On the Internet, each computer is assigned a unique IP address, however, the 32-bit address scheme from IPv4 allows for just over 4 billion addresses. With the growing number of devices connected to the web, we have run out of IP addresses. IPv6 is a 128-bit IP address. In the example above, the ip6 address begins with an ff, this should look familiar as some of the digits are represented in hexadecimal rather than binary. A full IPv6 address could be written like this 3ffe:1900:4545:3:200:f8ff:fe21:67cf.</blockquote>

<blockquote>Q7: In your own words, explain why a 32-bit address scheme provides just over 4 billion addresses. How many unique addresses does a 128-bit scheme provide for?</blockquote>

12. Test the Apache installation using lynx and our local host. At the prompt type, `lynx localhost`. You should see the Apache2 Debian Default Page

<blockquote>Q8: Open the page in the web browser by using localhost as the URL. Now try the IP address 127.0.1.1 and http://raspberrypi. What happens? Why?</blockquote>

13. Now we are going to configure the server using the `.conf` file (aka the configuration file).

14. At the command line use `sudo nano` to open `/etc/apache2/apache2.conf`
 
15. This file configures the behavior of our server. The commented lines explain the function of the commands that follow. 

16. Remember that if we were to set a static IP address (different from 127.0.0.1) and allow web traffic to find our page we would have other computers accessing ours. These settings control how our server reacts when handling requests from multiple machines.

17. We are going to add a few settings that would ensure that our Pi is not overloaded by requests on the web. Scroll down to the bottom of the file and add a line of blank space, then add the following lines:
```
# User Customization
  <IfModule mpm_prefork_module> StartServers 2
  MinSpareServers 6
  MaxSpareServers 12
  MaxClients 30
  MaxRequestsPerChild 3000
  </IfModule>
```
18. Save the file and restart your server using `sudo service apache2 restart`.

<blockquote>What’s going on here? We have a limited amount of memory and processing power on our machines (remember a server is just a computer), so we need to control the traffic on our machine. While the firewall controls access to the machine, this conf file and the piece of code that we added controls the way the machine acts when it  receives requests – that is this file defines how your server performs when receiving multiple requests. It’s like tuning an engine. For details on what we have just added see https://www.linode.com/docs/websites/apache-tips-and-tricks/tuning-your-apache-server and http://httpd.apache.org/docs/2.2/mod/mpm_common.html#startservers.</blockquote>

## Task 5: Setting up a Virtual Host

Virtual hosting is a method for hosing multiple domain names on a server. A domain name is the human readable designation for the IP address. For example, we used `localhost` and `raspberrypi` to resolve to the IP address 127.0.1.1. On the World Wide Web, you must first register a domain name as a place holder because these particular configurations of letters and numbers have been purchased. The conventions for naming domains are dictated by the Domain Name System. See https://www.w3.org/wiki/Getting_your_content_online for more details. A Domain Name Server is like a phonebook, these servers translate the domain name to the unique IP address for your server. Domain Names allow us to use more memorable representations of the domain names so that we don’t have to remember that string of numbers.

Our server will not be available to the wider WWW, so we don’t have to worry about purchasing a domain name or paying for hosting. However, using the `localhost` IP address we can rename our host. We have already established that `raspberrypi` is the default host. 

1. Let’s add a host `mysite.dev`. (You can use any name that you would like here. I suggest using the .dev extension rather than .com, .org, .net, etc so that your web browser does not attempt to connect to already existing sites out there in the world.)

2. Reopen the hosts file `sudo nano /etc/hosts`

3. Now we will append the file a bit. The entire block of 127.0.0.0 addresses (127.0.0.1 through 127.255.255.255) have been reserved for loopback to local hosts, meaning that we can assign different domain names to different IP localhost IP addresses. So, let’s add mysite.dev to our hosts file.

4. Below the `raspberrypi` line, add `127.0.1.2 mysite.dev`. Exit and save.

5. Check to make sure that your new domain name works using `lynx mysite.dev`. You should see the default Apache page.

6. The web browser will try to resolve to the www, so make sure to use the full URL http://mysite.dev/ when testing your domain name in the GUI web browser.

7. At the moment, the system is defaulting to Apache’s default web page. We will want to add our own content, but we need a place to do this.

8. At the command prompt, navigate to the `www` folder `cd /var/www/html`.

9. Use `ls` to see the contents. You should see an `index.html` file.

10. Use `sudo nano` to open this file.

<blockquote>Q9: Can you describe what you are looking at here? Hint: Take a look at the information between the <title> tags.</blockquote>
  
11. What you see here is the html file containing the Apache Default page. This is the file that opens when you request to see `mysite.dev`. Exit `nano`.

12. We are going to create a new folder to hold our web files. Navigate back to the `www` folder using `cd ..`

<blockquote>Q10: What does this command do?</blockquote>

13. We should now be back in the `/var/www folder`. You can confirm this by looking at the command prompt. You should see `pi@raspberrypi:/var/www$`.

14. Create a new directory with the name of our site using `sudo mkdir mysite.dev`

15. Navigate to the directory with your new folder `cd mysite.dev`

16. We are going to make another directory that will hold all of the documents that we would like to share with the public on our site. `sudo mkdir public_html`

17. Navigate to the directory with your newly-created folder `cd public_html`.

18. Use the print working directory command (`pwd`) to see the current directory. The command should return `/var/www/mysite.dev/public_html`.

19. Create a simple HTML page for our new `mysite.dev` site using `sudo nano index.html`

20. Add some content to the <body> section of this new HTML file.
```HTML
  <html>
    <body>Welcome to mysite.dev</body>
  </html>
```
21. Save and close the file. We can update this later with your files from the HTML project. Don’t worry about it for now.

22. Open `mysite.dev` in lynx. You’ll still see the Apache Default page. 

23. We have to tell new site that we would like to make available to the web. We do this by creating a new configuration file for our website. We’ll do this in the Apache `sites-available` file.

24. Use `sudo nano /etc/apache2/sites-available/mysite.conf` to open a new file.

25. In the new blank file, add the following:
```
<VirtualHost *:80>
  ServerAdmin webmaster@localhost 
  ServerName mysite.dev 
  ServerAlias www.mysite.dev
  DocumentRoot /var/www/mysite.dev/public_html 
  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
26. Here we are creating the virtual host for `mysite.dev`. We have the name of the server – `mysite.dev` instead of `raspberrypi` (the name of your computer). 

27. The alias is the URL `www.mysite.dev`. This is what the IP address resolves to. The Document Root tells Apache where the files for our site live. In this case it’s in the new directory that we just created.

28. Enable the site using the `command sudo a2ensite mysite.conf`

29. Restart Apache as prompted. Remember to use `sudo`.

30. Open `mysite.dev` in lynx. You should now see the text from the html file that you created a few steps ago.

## Task 6: Installing PHP

P in this case is for PHP. As indicated earlier the P can also stand for Python or Pearl. These are all programming languages. We have already used Python to learn some programming basics. Wordpress and other dynamic webpage platforms use PHP, so we’ll install it now.

1. Install php using `sudo apt-get command sudo apt install php7.3 libapache2-mod-php7.3 php-mysql -y`

<blockquote>NOTE: Pay close attention to the version of php that is installed. You will need to know the version number in future steps.</blockquote>

2. Open the php initialization file `sudo nano /etc/php/7.3/apache2/php.ini`. If you installed a different version, replace the 7.3 with the correct version number.

3. Check to ensure that the following configuration defaults are correct. You may need to uncomment and modify some lines (you will have to scroll through the file, these lines do not appear together):
```php
max_execution_time = 30 
memory_limit = 128M 
display_errors = Off 
log_errors = On
error_log = /var/log/php/error.log 
register_globals = Off 
;The register_globals line may not exist. That’s OK
```

<blockquote>Use <code><control><w></code> to find text within nano. Repeat the command and press <code><enter></code> to find multiple instances of the same string.</blockquote>
  
<blockquote>Lines that being with a “;” are the commented lines.</blockquote> 

4. Exit and save any changes.

5. You may have had to enter the `error_log` location. Check to make sure that it exists using `cd /var/log/php`

6. If you receive an error, try to resolve this one on your own: 
  a. navigate to the `/var/log/` directory
  b. use `mkdir` to create a `php` directory
  c. navigate to the newly-created `php` directory
  d. use `touch` to create an `error.log` file in the newly created `php` directory.

<blockquote>For help with the commands search the web, or use explainshell.com, or your classmates for help.</blockquote>

7. Restart the server using `service apache2 restart`

8. Test the php install to ensure that it is working properly.

9. Create a new file called `index.php` in the `/var/www/mysite.dev/public_html` folder.
 
10. Enter the following php code into the blank file:
```php
<?php
  echo '<h1>Hello World</h1>';
?>
```

<blockquote>Do you want to try some dynamic PHP? Add the line <code><?php echo date('Y-m-d H:i:s'); ?></code> to the <code>index.php</code> file. What do you think this command will do?</blockquote>

11. You have just created a basic php file. If you look at the contents of the `public_html` folder, you should now have an `index.html` and `index.php` file in this folder.

12. Now open http://mysite.dev/index.php in lynx. You should see `Hello World` on the screen.

<blockquote>Q11: Here we have called mysite.dev/index.php in the browser. Describe in your own words what we are doing with this command.</blockquote>

13. If you just navigate to `mysite.dev` you will see the html file that we created earlier. This is because Apache is defaulting to html. We have to tell the server to default to php by changing the settings. We don’t need to worry about this now.

## Task 7: Installing MySQL

At this point we’ve installed the L, A, and P. MySQL (pronounced *My Sequel*) is the final step in creating our LAMP stack. MySQL is an open source database management system. Many blogs and other content management systems used relational databases or organize data such as blog posts. MySQL is used for this purpose.

<blockquote>What is MySQL? What is a Database? What is SQL? http://www.thesitewizard.com/faqs/what-is-mysql-database.shtml</blockquote>

1. Start with `sudo apt-get install mariadb-server -y`

2. When the install is complete, restart apache `sudo service apache2 restart`

3. We need to take a few steps to secure our MySQL databases.
  a. run `sudo mysql_secure_installation`
  b. hit <enter> when prompted for a password. We haven't set up a password yet.
  c. type <y> when prompted to change the root password
  d. enter a password **STORE THIS PASSWORD FOR LATER ACCESS**
    * Note: your cursor will not move as you type
  
<blockquote>Did you forget your password? Use these instructions: https://support.rackspace.com/how-to/mysql-resetting-a-lost-mysql-root-password/</blockquote>

<blockquote>NOTE: the default answer is in caps, in this case Y. To choose the default press <Enter>.</blockquote>
  
4. When prompted, select the following options:
  - remove anonymous users? `<Y>`
  - disallow root login remotely? `<Y>`
  - remove test database and access to it? `<Y>`
  - reload privilege tables now? `<Y>`

5. Your MySQL installation is now secure.

6. Next, we are going to create a database and grant other users permission to use the databases.
  a. Log in using `mysql –u root –p`. This commands requests to login as the root user with a password.
  b. When prompted, enter your mysql password.
  c. You should now see the mysql prompt `mariadb>`. This means that we are now working within MySQL and not the Bash Shell.

7. Now we are going to create a new database and a new user that can have access to that database.
  a. First create a new database called csc2020: `create database csc2020;`

<blockquote>NOT: the semicolons (;) at the end of each of the following lines are crucial for ending the commands. If you press <enter> before typing the ; the command will not execute. Simply type the ; on the next line to execute the command.</blockquote>
  
  b. Now you’ll create a new user of the csc2020 called `csc_user` that is identified by a new password. 
  c. In this case substitute a password for the word password: `grant all on csc2020.* to ‘csc_user’ identified by ‘[INSERT A PASSWORD]’;`
  d. You have now configured MySQL and can pass on these database credentials to other users of your database. 
  e. To exit use the command `quit`.

8. Our last step is to install phpMyAdmin. This is a GUI interface that can be used for MySQL administration.
  a. Install using `sudo apt-get install phpmyadmin –y`
  b.	If prompted for the type of web server to reconfigure select “apache2” with the spacebar and press `<ENTER>`
    *	A yellow character will appear next to `apache2` when selected.
  c.	If prompted to configure the database with dbconfig-common select `<No>` with your arrow keys and press `<ENTER>`

9. Now we need to set up the `.htaccess` file for phpMyAdmin. `.htaccess` files are another type of configuration file used on Apache web servers. 
  a. We first have to make a change to the apache configuration file.
  b. Open the file in nano `sudo nano /etc/phpmyadmin/apache.conf`
  c. Under the directory section, add the line `AllowOverride All` under `Directory Index`
```php
  <Directory /usr/share/phpmyadmin> 
    Options FollowSymLinks 
    DirectoryIndex index.php 
    AllowOverride All
```

10. We have now allowed configuration with the `.htaccess` file. Now we can set up a user whose login would be required to access the phpmyadmin login page, adding another layer of security.
  a. Open the `.htaccess` file with nano `sudo nano /usr/share/phpmyadmin/.htaccess`
  b. Set up the user authorization within the blank `.htaccess` file with the following code:
```
AuthType Basic
AuthName "Restricted Files" 
AuthUserFile /etc/.htpasswd 
Require valid-user
```

<blockquote>AuthType: Refers to the type of authentication that will be used to check the passwords. The passwords are checked via HTTP AuthName: This is the text that will be displayed at the password prompt. You can type whatever you’d like between the “”.</blockquote>

<blockquote>AuthUserFile: This designates the path to the password file (we’ll create this next).</blockquote>

<blockquote>Require valid-user: Tells the .htaccess file that only users defined in the .htpasswd file can access the phpMyAdmin login screen</blockquote>

11. Now we need to create the `.htpasswd` file. Use the `htpasswd` command to place the `.htpasswd` file in the `/etc` directory. 

12. Make sure that your `.htaccess` file and the path to the file are the same. 

13. Be sure to put this somewhere where it is not accessible from the browser (i.e. do not use `/var/www/mysite.dev/public_html`).

14. Create a new user with the following command. Replace `username` with the user name that you would like to use: `sudo htpasswd –c /etc/.htpasswd username`

15. Provide and confirm a password. Remember to create a strong password.

16. Restart apache `sudo service apache2 restart`

17. You have just created a separate user name and password to access the phpMyAdmin login page.

<blockquote>Q12: Use the command <code>cat /etc/.htpasswd</code> to return the contents of the .htpasswd file that you just created. What do you see? This file contains an encrypted version of the user name and password that you just created. Why is this important?</blockquote>

<blockquote>.htpasswd: encrypts passwords using an version of the MD5 algorithm. To read more on .htpasswd see https://httpd.apache.org/docs/current/programs/htpasswd.html.</blockquote>

<blockquote>MD5: is an algorithm that creates a 128-bit hash value. This means that the original data (in this case your password) is converted into a 128 bit string that must be decoded by the computer.</blockquote>

18. Open the web browser and navigate to http://mysite.dev/phpmyadmin.

19. If your setup was successful you should be prompted for a user name and password in a pop-up before you are granted access to the phpMyAdmin site. This is the .htpasswd user and password that we just created.

20. Now you are at the phpMyAdmin login. Here you need to provide the username and password for the `csc2020` database.

21. If successful, you should now have access to phpMyAdmin. This tool allows you to administer your databases and users from a GUI interface rather than from the command line. We’ll revisit this later this term.

22. Logout by clicking on the door icon next to the home icon.

23. Congrats! You have now successfully installed a LAMP stack on your Pi. You can host webpages by placing .html or .php files into the public_html directory for the site. 

24. In our next project, we will install WordPress and explore how the elements of the L-A-M-P stack work together.

# Endnotes

<sup id="fn1">1. daemon, Seaarch SOA http://searchsoa.techtarget.com/definition/daemon<a href="#ref1" title="Jump back to footnote 1 in the text.">↩</a></sup>
<sup id="fn2">2. White, How Computers Work, 312.<a href="#ref2" title="Jump back to footnote 2 in the text.">↩</a></sup>

# Lab Notebook Questions

All of the required notebook questions are listed here. Be sure to answer each question completely, including an explanation of how you arrived at your answer.

Q1: Explain this set of commands in your own words.

Q2: Where do you commonly see HTTP? Why?

Q3: Now we have a basic firewall established. Can you explain what the firewall is doing in your own words? Would you set any additional rules? Use the [TCP/IP Ports and Protocols chart](http://www.pearsonitcertification.com/articles/article.aspx?p=1868080) to review common ports and services. You may also wish to set additional rules on your firewall using this chart.

Q4: Describe the differences between viewing websites in Lynx and through Firefox/Chrome/Safari/your favorite web browser.

Q5: What does the `–y` mean in this command? Use Explain Shell http://explainshell.com to find out.

Q6: The definition above states that the IP address is a 32-bit address, explain what this means in your own words (think back to the binary math project).

Q7: In your own words, explain why a 32-bit address scheme provides just over 4 billion addresses. How many unique addresses does a 128-bit scheme provide for?

Q8: Open the page in the web browser by using localhost as the URL. Now try the IP address 127.0.1.1 and http://raspberrypi. What happens? Why?

Q9: Can you describe what you are looking at here? Hint: Take a look at the information between the <title> tags.

Q10: What does this command do?

Q11: Here we have called mysite.dev/index.php in the browser. Describe in your own words what we are doing with this command.

Q12: Use the command `cat /etc/.htpasswd` to return the contents of the .htpasswd file that you just created. What do you see? This file contains an encrypted version of the user name and password that you just created. Why is this important?
