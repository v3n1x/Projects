19.09.2025

## Requirements

-   Dual core Processor (2 GHz)
-   4GB RAM
-   25GB or more disk space
-   NAT interface (to install the DVWA web server later we are going to
    change it)

The installation is simple, we are going with the default, do not forget to download OpenSSH.

Now we have a server set up and ready.

We can run the following command:

``` bash
sudo apt update
```

## Install Required Tools and DVWA

Now we are going to use the following commands to install the required
tools that we are going to use and then install DVWA into the
`/var/www/html` directory.

``` bash
sudo apt install -y apache2 mariadb-server mariadb-client php php-mysqli php-gd libapache2-mod-php nano unzip fping
cd /var/www/html
sudo wget https://github.com/digininja/DVWA/archive/master.zip
sudo unzip master.zip
mv DVWA-master DVWA
```

## Network Configuration

Now we can change the network interface to vmnet20. After that, we are
going to use the following command:

``` bash
sudo nano /etc/netplan/01-network-config.yaml
```

Put this inside the file:

``` yaml
network:
    version: 2
    renderer: networkd
    ethernets:
        ens33:
            addresses:
                - 10.0.20.10/24
            nameservers:
                addresses: [10.0.20.254]
            routes:
                - to: default
                  via: 10.0.20.254
```

After that, change the permissions on the file:

``` bash
sudo chmod 600 /etc/netplan/01-network-config.yaml
sudo netplan apply
```

Check if it worked:

``` bash
ip a
```

## DVWA Installation

First start the Apache service:

``` bash
sudo systemctl start apache2
sudo systemctl status apache2
```

After that copy the DVWA config file:

``` bash
cp config/config.inc.php.dist config/config.inc.php
```

### MariaDB Configuration

Connect to MariaDB:

``` bash
mariadb
```

Then create the database and user:

``` sql
create database dvwa;
create user dvwa@localhost identified by 'p@ssw0rd';
grant all on dvwa.* to dvwa@localhost;
flush privileges;
exit;
```

Test the login:

``` bash
mysql -u dvwa -pp@ssw0rd
```

Now go to `http://10.0.20.10/setup.php` and click on **Create/Reset
Database**.

You should now be able to visit:

    http://10.0.20.10/DVWA/

Login using:
- Username: `admin`
- Password: `password`

## Other DVWA Configuration

On the setup page we can see that we need to change some file
permissions and some settings to allow some of the labs.
Run these commands:

``` bash
chmod 777 /var/www/html/DVWA/hackable/uploads/
chmod 777 /var/www/html/DVWA/config
```

Then edit the PHP config file (found at `/etc/php/x.x/apache2/php.ini`)
and allow both:

    allow_url_fopen = On
    allow_url_include = On

## Apache Configuration

Now we want to set up DNS for the web application.
First change the Apache config file name and edit it:

``` bash
cd /etc/apache2/sites-available/
sudo cp 000-default.conf system.null.corp.conf
sudo nano system.null.corp.conf
```

Now enable the new configuration:

``` bash
sudo a2ensite system.null.corp.conf
sudo a2dissite 000-default.conf
sudo systemctl restart apache2
```

Then edit the `/etc/hosts` file and add your domain.

On the host machine (Windows), edit the file located at:
`C:\Windows\System32\drivers\etc\hosts`

Open Notepad as Administrator and add the IP of the website.

Now you can access it by the FQDN from your host machine.

## Done
