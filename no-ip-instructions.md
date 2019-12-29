Finished setting up your LAMP server? With your extra time you can put your website from the HTML project live on the web. First, we need to set up a domain. We can do this with a free service called No-IP.

The No-IP service to emulates a static IP address. A static address means that your machine is designated with an IP address that is always the same. When you connect to a network your devices are typically assigned a dynamic IP address, meaning that your IP address is different every time that you connect. 

This isn’t useful for a server as it always has to have the same address, like a phone number so that other computers can connect to it. No IP uses the Dynamic IP Update Client, a piece of software that monitors the changes in your IP address so that your server can always be found.

No IP has a free service that allows you to use a domain name for 30 days. We can use this option to get your Pi on the WWW.

# Task 1: Create a No-IP Account and Hostname

First, connect your Pi via Ethernet to your network. That is, use the cable rather than using WiFi.

Go to http://no-ip.com and create a free account. You’ll notice that you can customize a piece of your domain, but not the entire domain. With the paid service, you can purchase a domain like katiewalden.net.

You will be prompted for your IP address. To find your IP address on your pi, open a Terminal session and use `ip addr show` 

You will see a lot of information presented here. Look at `connection 2: eth0`. Look for `inet` followed by an IP address. This is your current IPv4 address.

# Task 2: Configure Your New Host

Next we need to reconfigure our hosts file so that our new domain name resolves to our website. We originally added `mysite.dev` to our hosts file. We’ll follow the same instructions to set up a new site on our server with the new domain name.

Open the hosts file using `sudo nano /etc/hosts`

Add a new line for `127.0.2.2` for your new No-IP domain. Mine is kwalden.ddns.net.

Check to make sure that your site works by opening it in the lynx browser. You should see the default Apache page.

Use `cd` to move into the `/var/www/` folder. Use `mkdir` to create a new folder using the name of your new domain name. For example, I used the command `mkdir kwalden.ddns.net`.

Use `cd` to move into the new directory that you just created. Use `mkdir` again to create a `public_html` folder. 

Use `cd` to move into the `public_html` folder and create a new `index.html` as a temporary page using `nano`.

Now we need to reconfigure the server with your new domain. 

Open the file with the command `sudo nano /etc/apache2/sites- available/yournewdomainname.conf.`

Here all you need to change is the `ServerName` and `ServerAlias` to match the domain information from No-IP. Mine now looks like this:
```
<VirtualHost *:80>
ServerAdmin webmaster@localhost 
ServerName kwalden.ddns.net 
ServerAlias www.kwalden.ddns.net
DocumentRoot /var/www/kwalden.ddns.net/public_html 
ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
 
Notice that the document root remains the same. This means that when you resolve to your new URL from No IP that the server will still open the files in the mysite.dev/public_html folder. 

Enable the site using `sudo a2ensite nameofyoursite`. Restart Apache as prompted.

# Task 3: Install the Dynamic Update Client

Next, we need to install the software on the Pi that will track the changes in our IP address. Your Pi is using a dynamic IP address, meaning that it changes every time you log on and off of a network.  The No-IP client tracks these changes so that you don’t have to enter a new IP address into the site each time you want your server to be available to the world. 

These instructions are modified from http://www.noip.com/support/knowledgebase/installing-the-linux-dynamic-update-client/.

Navigate to the `src` directory using `cd /usr/local/src`
 
Download the software using `wget http://www.no- ip.com/client/linux/noip-duc-linux.tar.gz`

Unzip or extract the files using `tar xzf noip-duc-linux.targ.gz`

<blockquote>Note that this is a .tar file and not a .zip file. They are both a means of archiving or compressing files, but each requires a different method to extract the files from the package.</blockquote>

Navigate to the `no-ip` folder using `cd no-ip-2.1.9-1`

Start the install process by running `make`

Run `make install` and follow the instructions. You’ll be asked to enter your log-in information and set an interval (in minutes) when the software will update your ip address. The default of 30 minutes is fine for our purposes.

Use `sudo noip2 -S` to confirm the software is working.

You should now be able to navigate to your new URL from any computer. Your Pi Server is on the open web!

# Task 4: Launch Your Website

When you navigate to your site, you are going to see the `index.html` file. 

Move your HTML files from the previous project to your `public_html` folder so that you can share your site with the class (and the world).

In the Terminal, navigate to the directory where you have saved your HTML site. 

Use `ls` to see the contents of the folder. You’ll need to move all of the files to the new `public_html` folder for the no-ip site.

Use `cd` to get back to the root directory. 

We can use `mv` to move the contents of the HTML project folder. 

My command looks like this: `sudo mv ~/Desktop/ExampleSite/* /var/www/kwalden.ddns.net/public_html`. You’ll need to substitute the path to your html files for the first piece and the path to your `public_html` folder for the second.
 
Use the `cd` command to move into the `public_html` folder. Mine is `cd /var/www/kwalden.ddns.net/public_html`. Run `ls` to see all of the files that we just moved.

Visit your new site on the web browser.

Remember, you Pi must be on and connected to the web for others to see your site.
