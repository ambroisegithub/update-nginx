# LEMP-stack
This repo contains how To Install Linux, Nginx, MySQL, PHP (LEMP stack) on Ubuntu 20.0 and some usefull commands about configuring files.
Reference:
https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-20-04-quickstart
## Step 1 — Install Nginx
Update your package manager cache and then install Nginx with:
```
sudo apt update
sudo apt install nginx
```
# Configure firewall & create mysql user
https://www.cyberciti.biz/faq/how-to-configure-firewall-with-ufw-on-ubuntu-20-04-lts/
mYsql user
https://www.digitalocean.com/community/tutorials/how-to-create-a-new-user-and-grant-permissions-in-mysql
Once the installation is finished, you’ll need to adjust your firewall settings to allow HTTP traffic on your server. Run the following command to allow external access on port 80 (HTTP):
```
sudo ufw allow in "Nginx"
```
With the new firewall rule added, you can test if the server is up and running by accessing your server’s public IP address or domain name from your web browser. You’ll see a page of Nginx welcome page
## Step 2 — Install MySQL
We’ll now install MySQL, a popular database management system used within PHP environments.

Again, use apt to acquire and install this software:
```
sudo apt install mysql-server
```
When the installation is finished, it’s recommended that you run a security script that comes pre-installed with MySQL. Start the interactive script by running:
```
sudo mysql_secure_installation
```
This will ask if you want to configure the VALIDATE PASSWORD PLUGIN. Answer Y for yes, or anything else to continue without enabling. If you answer “yes”, you’ll be asked to select a level of password validation.

Your server will next ask you to select and confirm a password for the MySQL root user. Even though the default authentication method for the MySQL root user dispenses the use of a password, even when one is set, you should define a strong password here as an additional safety measure.

For the rest of the questions, press Y and hit the ENTER key at each prompt.
## Step 3 — Install PHP
To install the php-fpm and php-mysql packages, run:
```
sudo apt install php-fpm php-mysql
```
## Step 4 — Configure Nginx for PHP
In this guide, we’ll set up a domain called your_domain, but you should replace this with your own domain name.

On Ubuntu 20.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html. While this works well for a single site, it can become difficult to manage if you are hosting multiple sites. Instead of modifying /var/www/html, we’ll create a directory structure within /var/www for the your_domain website, leaving /var/www/html in place as the default directory to be served if a client request doesn’t match any other sites.

Create the root web directory for your_domain as follows:
```
sudo mkdir /var/www/your_domain
```
Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:
```
sudo chown -R $USER:$USER /var/www/your_domain
```
Then, open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Here, we’ll use nano:
```
sudo nano /etc/nginx/sites-available/aimedidierm.com
```
This will create a new blank file. Paste in the following bare-bones configuration:
```
server {
    listen 80;
    server_name your_domain www.your_domain;
    root /var/www/your_domain;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```
When you’re done editing, save and close the file. If you’re using nano, you can do so by typing CTRL+X and then y and ENTER to confirm.

Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:
```
sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```
This will tell Nginx to use the configuration next time it is reloaded. You can test your configuration for syntax errors by typing:
```
sudo nginx -t
```
If any errors are reported, go back to your configuration file to review its contents before continuing.

When you are ready, reload Nginx to apply the changes:
```
sudo systemctl reload nginx
```
Your new website is now active, but the web root /var/www/your_domain is still empty. Create an index.html file in that location so that we can test that your new server block works as expected:
```
nano /var/www/your_domain/index.html
```
Include the following content in this file:
```
<html>
  <head>
    <title>your_domain website</title>
  </head>
  <body>
    <h1>Hello World!</h1>

    <p>This is the landing page of <strong>your_domain</strong>.</p>
  </body>
</html>
```
Now go to your browser and access your server’s domain name or IP address, as listed within the server_name directive in your server block configuration file:
```
http://server_domain_or_IP
```
You will see the page we created above.
## Step 5 — Test PHP with Nginx
We’ll now create a PHP test script to confirm that Nginx is able to handle and process requests for PHP files.

Create a new file named info.php inside your custom web root folder:
```
nano /var/www/your_domain/info.php
```
This will open a blank file. Add the following content inside the file:
``` php
<?php
phpinfo();
?>
```
When you are finished, save and close the file.

You can now access this page in your web browser by visiting the domain name or public IP address you’ve set up in your Nginx configuration file, followed by /info.php:
```
http://server_domain_or_IP/info.php
```
You will see a web page containing detailed information about your server.
After checking the relevant information about your PHP server through that page, it’s best to remove the file you created as it contains sensitive information about your PHP environment and your Ubuntu server. You can use rm to remove that file:
```
sudo rm /var/www/your_domain/info.php
```
## Step 6 — Install php Myadmin
Now you can install phpMyAdmin by running the following command:
```
sudo apt install phpmyadmin

```
During the installation process, you will be prompted to choose a web server (either Apache or Lighttpd) to configure. phpMyAdmin can automatically make a number of configuration changes to ensure that it works correctly with either of these web servers upon installation. However, because you are using Nginx as a web server you shouldn’t choose either of these options. Instead, press TAB to highlight the <Ok> and then press ENTER to continue the installation process.

Next, you’ll be prompted whether to use dbconfig-common for configuring the application database. Select <Yes>. This will set up the internal database and administrative user for phpMyAdmin. You will be asked to define a new password for the phpmyadmin MySQL user, but because this isn’t a password you need to remember you can leave it blank and let phpMyAdmin randomly create a password.
### Note: 
Assuming you installed MySQL by following Step 2 of the prerequisite LEMP stack tutorial, you may have decided to enable the Validate Password plugin. As of this writing, enabling this component will trigger an error when the phpMyAdmin installation process attempts to set a password for the phpmyadmin user
To resolve this, select the abort option to stop the installation process. Then, open up your MySQL prompt:
```
sudo mysql
    
```
From the MySQL prompt, run the following command to disable the Validate Password component. Note that this won’t actually uninstall it, but just stop the component from being loaded on your MySQL server:
```
UNINSTALL COMPONENT "file://component_validate_password";
    
```
Following that, you can close the MySQL client:
```
exit
    
```
Then try installing the phpmyadmin package again and it will work as expected:
```
sudo apt install phpmyadmin
    
```
Once phpMyAdmin is installed, you can open the MySQL prompt once again with sudo mysql or mysql -u root -p and then run the following command to re-enable the Validate Password component:
```
INSTALL COMPONENT "file://component_validate_password";
    
```
Once the apt install command completes, phpMyAdmin will be fully installed. However, for the Nginx web server to find and serve the phpMyAdmin files correctly, you’ll need to create a symbolic link from the installation files to Nginx’s document root directory. If you followed the prerequisite LEMP stack tutorial, your Nginx installation’s document root is /var/www/your_domain/
```
sudo ln -s /usr/share/phpmyadmin /var/www/your_domain/phpmyadmin
    
```
Your phpMyAdmin installation is now operational. To access the interface, go to your server’s domain name or public IP address followed by /phpmyadmin in your web browser:
```
https://server_domain_or_IP/phpmyadmin
    
```
## Create MSQL user
https://www.digitalocean.com/community/tutorials/how-to-create-a-new-user-and-grant-permissions-in-mysql
