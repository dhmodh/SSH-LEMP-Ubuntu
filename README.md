# A Relatively Secure LEMP Server Stack Setup in Ubuntu 20.04 LTS

> By _Dishant Modh_

- [A Relatively Secure LEMP Server Stack Setup in Ubuntu 20.04 LTS](#a-relatively-secure-lemp-server-stack-setup-in-ubuntu-2004-lts)
	- [Introduction](#introduction)
		- [Specifications/Target](#specificationstarget)
	- [The Installation](#the-installation)
		- [Update APT](#update-apt)
		- [Installing Nginx](#installing-nginx)
		- [Installing MySQL](#installing-mysql)
		- [Installing PHP](#installing-php)
	- [Configurations](#configurations)
		- [Configuring Nginx to Use the PHP Processor](#configuring-nginx-to-use-the-php-processor)
		
		
## Introduction

In this guide, **LEMP** stands for **Linux**, **Nginx** (pronounced as
Engine-X), **MySQL** and **PHP** (PHP Hypertext Preprocessor). Also, as a
bonus.

I have gone through many websites in order to learn even just the basics of
setting up a LEMP server. Here I have created a through guide to help someone 
relatively new to setup a powerful LEMP machine easily and effortlessly. 
I hope this guide comes to someone's rescue.

The instructions on this guide has been tested to work on a fresh install of
Ubuntu 20.04. Also, the system was fully updated before we started with the 
LEMP setup procedure.

For any other platform, this guide should still serve as a useful resource. The
"mini goals" are well defined in various paragraphs. And one can easily look
elsewhere for specific instructions for that platform to achieve same "mini
goal", one at a time.

For a system already running other version of server stacks (example LAMP),
some additional steps (like removing those packages or disabling them) might
be required. That is beyond the scope of this guide.

### Specifications/Target

* **Ubuntu**                v20.04
* **Nginx**                 v1.18.0
* **MySQL**                 v8.0.26
* **PHP**                   v7.4.3

## The Installation

Let's finally begin the actual installations processes.

_During the installation of any package, when faced with any unsure prompt,
just go with the default option/action._

### Update APT

First, we want to make sure we have the latest records in our local packages
registry. Let's run the following command in the terminal like so.

```shell
sudo apt update
```

### Installing Nginx

First thing we’re going to install is the server called Nginx.

```shell
sudo apt install nginx
```

If you do not have a domain name pointed at your server and you do not know your server’s public IP address, you can find it by running the following command:

```shell
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```
This will print out a few IP addresses. 

Type the address that you receive in your web browser and it will take you to Nginx’s default landing page:

```shell
http://server_domain_or_IP
```

### Installing MySQL

Now we want to install the database management system (DBMS). We choose MySQL.

```shell
sudo apt install mysql-server
```

When the installation is finished, this script will remove some insecure default settings and lock down access to your database system. Start the interactive script by running:

```shell
sudo mysql_secure_installation
```

When you’re finished, test if you’re able to log in to the MySQL console by typing:

```shell
sudo mysql
```
To exit the MySQL console, type:

```
exit
```

### Installing PHP

You have Nginx installed to serve your content and MySQL installed to store and manage your data. Now you can install PHP to process code and generate dynamic content for the web server.

```
sudo apt install php-fpm php-mysql
```

You now have your PHP components installed. Next, you’ll configure Nginx to use them.

## Configurations

Time to make the configuration and host more than one domain on a single server.

### Configuring Nginx to Use the PHP Processor

When using the Nginx web server, we can create server blocks to encapsulate configuration details and host more than one domain on a single server. In this guide, we’ll use your_domain as an example domain name.

Create the root web directory for your_domain as follows:

```
sudo mkdir /var/www/your_domain
```

Assign ownership of the directory with the $USER environment variable, which will reference your current system user:

```
sudo chown -R $USER:$USER /var/www/your_domain
```

Open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Here, we’ll use vim:

```
sudo vim /etc/nginx/sites-available/your_domain
```

Paste in the following bare-bones configuration:
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
Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:

```
sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```

Unlink the default configuration file from the /sites-enabled/ directory:

```
sudo unlink /etc/nginx/sites-enabled/default
```

If any errors are reported, go back to your configuration file to review its contents before continuing.

When you are ready, reload Nginx to apply the changes:

```
sudo systemctl reload nginx
```
