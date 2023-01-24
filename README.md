## **LEMP-STACK-IMPLEMENTATION**
---
---

The steps in implementing LEMP (Linux, Nginx, MySQL and PHP) is similar to what we did in LAMP stack. Here we will use an alternative server called [Nginx](https://nginx.org/en/) . 

We will go through the six steps i used in implementing LEMP stack.

### **1. INSTALLING NGINX** ###

- We start with updating our server packages index and installing nginx
    
    `sudo apt update`

    `sudo apt install nginx`

- To verify nginx was successfully installed and running, run the below code

    `sudo systemctl status nginx`

![NGINX STATUS](./images/NGINX%20STATUS.PNG)

- Before we can receive any traffic by our Web Server, we need to open [TCP port 80](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers) which is default port that web broWsers use to access web pages in the Internet .

- Let us try how we can access it locally in Ubuntu

    `curl http://127.0.0.1:80`

![NGINX SERVER RUNNING](./images/NGINX%20SERVER%20RUNNING.PNG)

### **2. INSTALLING MYSQL** ###

- We use `apt` to acquire and install this software

    `sudo apt install mysql-server`

- When the installation is finished, log in to the mySQL console by typing

    `sudo mysql`

- This will connect to the MySQL server as the administrative database user root

- It’s recommended that you run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system.

    `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

- Exit the MySQL shell with

    `mysql> exit`

- start the interactive script by running

    `sudo mysql_secure_installation`

- use a strong password, and read through the prompt

    `sudo mysql -p`

    `mysql> exit`

### **3. INSTALLING PHP** ###

- We have Nginx installed to serve your content and MySQL installed to store and manage your data. Now you can install PHP to process code and generate dynamic content for the web server.

- We need to install php-fpm, which stands for PHP fastCGI process manager. We also need php-mysql a PHP module that allows PHP to communicate with MySQL-based databases.

- To install these 2 packages at once run

    `sudo apt install php-fpm php-mysql`

### **4. CONFIGURING NGINX TO USE PHP PROCESSOR** ###

- Create the root web directory for your domain

    `sudo mkdir /var/www/LEMPproject`

- Next assign ownership of the directory with the $USER environment variable, which will reference your current system user

    `sudo chown -R $USER:$USER /var/www/LEMPproject`

- Next we will open a new configuration file in Nginx sites-available directory and paste the below block of code.

    `sudo vim /etc/nginx/sites-available/LEMPproject`

```
    #/etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```
- Ensure to save and close your editor when done.

- Activate your configuration by linking to the config file from Nginx's sites-enabled

    `sudo ln -s /etc/nginx/sites-available/LEMPproject /etc/nginx/sites-enabled/`

- We can test this configuration using the code below.

    `sudo nginx -t`

 ```
 ubuntu@ip-172-31-81-216:~$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
 ```

 - We also need to disable default Nginx host that is currently configured to listen on port 80, for this run

    `sudo unlink /etc/nginx/sites-enabled/default`

- When done, we reload Nginx to apply changes.

    `sudo systemctl reload nginx`

- Since the web root is empty /var/www/LEMPproject, we will create an index.html in that location so that we can test the new server block works...

    `sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`

- Now let's go to a browser to test

    `http://<public IP Address>:80`

![NGINX SERVER RUNNING ON INDEX](./images/NGINX%20SERVER%20RUNNING%20ON%20INDEX.PNG)


### **5. TESTING PHP WITH NGINX** ###

- At this point, LEMP stack is completely installed and fully operational 

- We will crete a test PHP file in our root document.

    `sudo vim /var/www/LEMPproject/info.php`

- Type or paste the below code into the new file

    `<?php phpinfo(); ?>`