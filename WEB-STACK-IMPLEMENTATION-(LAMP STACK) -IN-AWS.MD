


# EC2 INSTANCE




1. EC2 instance of t2.micro type and Ubuntu server 20.04LTS(HVM) was lunched in us-east-1 region using the AWS console.


![alt text](</PHOTOS/Screenshot (37).png>)
   
2. Creating of SSH key pair named key to access the instance.
   
3. the private ssh key that got downloaded was located, permission was changed for private file and it was used then to connect the instance by running.
  

`chmod 600 key.pem` 

`ssh -i key.pem ubuntu@52.6.230.17`


![alt text](<../steghub-DevOps-assignments/PHOTOS//Screenshot (3)-3.png>)

# Install Apache and Update the Firewall


1. update a list of packages in package manager

`sudo apt update`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (5).png>)


`sudo apt upgrade -y`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (6).png>)



2. Run apache2 package installation

`sudo apt install apache2 -y`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (8).png>)

1. Verify that apache is running on as a service on the OS.

`sudo systemctl e3nable apache2`

`sudo systeml status apache2`

if it is green and running, then apache2 is installed correctly

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (9).png>)

4. The server is running and can be accessed locally in the ubuntu shell by running this command below;

`curl http://localhost:80`
`or`
`curl http://127.0.0.1:80`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (10).png>)

5. Test with the public IP address if the Apache HTTP server can respond to the request from the internet using the Url on a browser.

`http://52.6.230.17:80` 

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (16).png>)

then another way to retrieve the public ip address other than checkthe aws console

`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`

after running the command above, it gave erro 401-unauthorized output.

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (11).png>)

troubleshooting the error message, the following changes will be made in AWS console:

1. Actions>Instance settings> Modify instance metadata options.
2. Change the IMDSV2 from Required to Optional.
  

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (12).png>)

run the command again, the error message wont display again

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (13).png>)

# install MySQL


1. install a relational database (RDB)

`sudo apt install mysql-server`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (14).png>)

when installing you have to comfirm by typing y and then Enter.

2. enable and verify that mysql is running with the command below

`sudo systemctl enable --now mysqsl`

` sudo systemctl status mysql`

![alt text](<../steghub-DevOps-assignments/PHOTOS/mysql pix.png>)

3. log into mysql console

`sudo mysql`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (17).png>)

`ALTERUSER'root'@'localhost' IDENTIFIED WITH mysql_native_password BY'PassWord.1';`


![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (17)-1.png>)



it connects the mysql server as the administrative databaseuser root infered by the use of sudo when running the command.



4. Set a password for rootuser using mysql_native_password as default authentication method.

the user password was defined as 

exit the Mysql shell

`exit`

1. Run an interactive script to secure Mysql

the security script comes pre installed with mysql, it also removes some insecure settings and lockdwon access to the data base

`sudo mysql_secure_installation`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (18).png>)

whether the validation password plugin is set up ,the server will ask to select and cnfirm a password for mysql root user.

6. log into mysql console after changing the root user. 

`sudo mysql -p`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (20).png>)


7. a command prompt for password was noticedafter running this command

Exit Mysql Shell

    `exit`

#  INSTALL PHP 

Apache is  installed to serve your content and MySQL installed to store and manage your data. PHP is the component of our setup that will process code to display dynamic content to the end user. 

to install the 3 packages once and run it

`sudo apt install php 
libapache2-mod-php php-mysql`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (22).png>)

if the installation is finished then run the following command 

`php -v`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (23).png>)

lamp stack is completely installed here and operational

# create a virtual host for the website using Apache
Create the directory for projectlamp using 'mkdir' command 

`sudo mkdir /var/www/projectlamp`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (23)-1.png>)

 assign ownership of the directory with the $USER environment variable, which will reference the current system user:

 `sudo chown -R $USER:$USER /var/www/projectlamp`

 
![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (23)-2.png>)

 create and open a new configuration file in apaches 'site avaliable' directory using vim.

 `sudo vi /etc/apache2/sites-available/projectlamp.conf`

 ![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (26).png>)

paste the following bare-bones configuration below;



```

<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (24)-1.png>)

1. show the new file in the sites-available directory


` sudo ls /etc/apache2/sites-available`
`000-default.conf  default-ssl.conf  projectlamp.conf`

With this VirtualHost configuration, we’re telling Apache to serve projectlamp using /var/www/projectlampl as its web root directory

4. Enable the new  virtual host

`sudo a2ensite projectlamp`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (27)-2.png>)

5. Disable apache’s default website.

this is because Apache’s default configuration would overwrite your virtual host , it is requried if a custom domianis not being used.

`sudo a2dissite 000-default`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (27)-1.png>)

 To make sure your configuration file doesn’t contain syntax errors, run:

 `sudo apache2ctl configtest`

 Reload apache for changes to take effect

 `sudo systemctl reload apache2`

 ![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (28).png>)

 Your new website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that we can test that the virtual host works as expected:

 ```
 sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
 ```


 open your website URL using IP address:

 `http://52.91.29.202:80`

 ![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (29).png>)

 open thwe website with public dns name (port is optional)

 `http://<Public-DNS-Name>:80`

 # Enable PHP on the website
With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative message to visitors. Because this page will take precedence over the index.php page, it will then become the landing page for the application. Once maintenance is over, the index.html is renamed or removed from the document root, bringing back the regular application page. if the behavior needs to be changed, /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:

Open the directive;

`sudo vim /etc/apache2/mods-enabled/dir.conf`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (31).png>)

```


<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```




Reload Apache

`sudo systemctl reload apache2`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (31)-1.png>)


we will create a PHP script to test that PHP is correctly installed and configured on your server.

`vim /var/www/projectlamp/index.php`

![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (34)-2.png>)

This will open a blank file. Add the following text, which is valid PHP code, inside the file


`<?php`
`phpinfo();`




Now refresh the page


![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (32)-1.png>)


This page provides information about your server from the perspective of PHP. It is useful for debugging and to ensure that your settings are being applied correctly.

After checking the relevant information about your PHP server through this page, it’s best to remove the file you created as it contains sensitive information about your PHP environment -and your Ubuntu server. You can use rm to do so:

 `sudo rm /var/www/projectlamp/index.php`

 ![alt text](<../steghub-DevOps-assignments/PHOTOS/Screenshot (36).png>)


 Summary

 Lamp stands for Linux, Apache, MYSQL, PHP, together they provide a set of software technologies that developers used for building and delivering of high-performance  web applications. each component contributes essential  capabilities to stack 