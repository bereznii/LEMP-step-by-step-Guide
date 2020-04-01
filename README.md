# LEMP install on Ubuntu 18.04

After setting up LAMP and LEMP on multiple servers, I found a set of perfectly working guides. Googling same things each time is irretional, so I decided to build a step-by-step guide for myself. Maybe it will be useful for someone.

## Getting Started

These instructions will get you through each step of installation process of LEMP (Linux, Nginx, MySQL and PHP) stack on clean machine with Ubuntu 18.04. Also see 'Laravel App Deployment' for guide to deploy laravel app easily.

### Ubuntu users

Working as root is a bad practice, so I prefer to create new user and add him to sudo group.

```
$ adduser dev
$ usermod -aG sudo dev
$ usermod -aG www-data dev
```

Also, I'm turning off pubkey authentication, and turn on password one to be able enter server from different machines easily.

```
$ sudo nano /etc/ssh/sshd_config
```

In opened file make sure you have following lines:
* PasswordAuthentication yes
* PubkeyAuthentication no
* PermitRootLogin yes
Restart sshd to apply changes.

```
$ sudo service sshd restart
```

Very important to not forget add password to your root user!

```
$ passwd
```

### Nginx

I execute steps in following order.

```
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install nginx
```
Configuring firewall to accept requests both to both HTTP and HTTPS. Also, to still accept request from OpenSSH to be able to enter server. 

```
$ sudo ufw allow 'Nginx Full'
$ sudo ufw allow 'OpenSSH'
$ sudo ufw enable
```

To check active rules.
```
$ sudo ufw status
```

### MySQL

```
$ sudo apt install mysql-server
$ sudo mysql_secure_installation
```

Last command will take you through a series of prompts where you can make some changes to your MySQL installation’s security options. I, personally, prefer this sequence of answers n-y-n-y-y.
Next, you should enter mysql terminal, add new password for root user, create new user, and flush priveleges.

```
$ sudo mysql
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
mysql> CREATE USER 'dev'@'localhost' IDENTIFIED BY 'password';
mysql> GRANT ALL PRIVILEGES ON * . * TO 'dev'@'localhost';
mysql> FLUSH PRIVILEGES;
mysql> exit;
```

### PHP

Run these command to install PHP7.4 with all needed extensions.

```
$ sudo apt install software-properties-common
$ sudo add-apt-repository ppa:ondrej/php
$ sudo apt update
$ sudo apt install php7.4-fpm
$ sudo apt install php7.4-common php7.4-mysql php7.4-xml php7.4-xmlrpc php7.4-curl php7.4-gd php7.4-imagick php7.4-cli php7.4-dev php7.4-imap php7.4-mbstring php7.4-opcache php7.4-soap php7.4-zip php7.4-intl -y

```

### Composer

```
$ sudo apt install curl php-cli php-mbstring git unzip
$ cd ~
$ curl -sS https://getcomposer.org/installer -o composer-setup.php
```
Go to https://composer.github.io/pubkeys.html and copy 'Installer checksum (SHA-384)'.
```
$ HASH={paste copied hash}
$ php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
```
Previous command should output 'Installer verified'.

```
$ sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
$ composer - check if installation succeed
```

### Swap file

```
$ sudo fallocate -l 1G /swapfile
$ sudo chmod 600 /swapfile
$ sudo mkswap /swapfile
$ sudo swapon /swapfile
$ sudo nano /etc/fstab - paste there this 'line /swapfile swap swap defaults 0 0'
$ sudo swapon --show - to check if succeed
```

### Add Nginx config for project

Paste here nginx config from official laravel documentation https://laravel.com/docs/7.x/deployment.
```
$ sudo nano /etc/nginx/sites-available/projectname
```

```
$ sudo ln -s /etc/nginx/sites-available/projectname /etc/nginx/sites-enabled/
$ sudo unlink /etc/nginx/sites-enabled/default
```

To test nginx config file syntax and reload it if OK.
```
$ sudo nginx -t
$ sudo systemctl reload nginx
```

### Laravel project deployment from github repository (may differ for Gitlab and Bitbucket).

Create directory for project.
```
$ sudo rm -R html/
$ sudo mkdir -p /var/www/bereznii.me
$ sudo mkdir -p /var/www/bereznii.me
$ sudo chmod -R 755 bereznii.me/
```

Clone project into created directory.
```
$ cd /var/www/bereznii.me
$ git clone https://github.com/bereznii/personal-website.git . 
```

Copy .env file and configure app environment variables.
```
$ cp .env.example .env 
```

Install all dependencies, generate app key, clear config to apply new one, run migrations.
```
$ composer update
$ php artisan key:generate
$ php artisan config:clear
$ php artisan migrate
```

I’m not devops, so for now these permissions settings will be enough (https://stackoverflow.com/questions/23411520/how-to-fix-error-laravel-log-could-not-be-opened).
```
$ sudo chown -R $USER:www-data storage
$ sudo chown -R $USER:www-data bootstrap/cache
$ chmod -R 775 storage
$ chmod -R 775 bootstrap/cache
```

Setup Laravel Scheduler.
```
$ crontab -e
$ paste * * * * * cd /path-to-your-project && php artisan schedule:run >> /dev/null 2>&1
```

To avoid further possible problems with permissions it is possible to implement such (awful) trick. Just add following line to sudo crontab:
```
*/5 */1 * * * chmod -R 775 /var/www/bereznii.me/storage >/dev/null 2>&1
*/5 */1 * * * chmod -R 775 /var/www/bereznii.me/bootstrap/cache >/dev/null 2>&1
*/5 */1 * * * chown -R dev:www-data /var/www/bereznii.me/storage >/dev/null 2>&1
*/5 */1 * * * chown -R dev:www-data /var/www/bereznii.me/bootstrap/cache >/dev/null 2>&1
```

## Authors

* **Dmytro Bereznii** - me, PHP developer

See also my other [projects](https://github.com/bereznii).
