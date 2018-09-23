
# Linux Configuration project

This deploy of last project in Full-Stack Web Developer Nanodegree

# Server

IP Public Addess : 18.184.74.60 

SSH port :2200

# Configuration
## Add user
Add user `grader` with command: `sudo adduser grader`

## Add user grader to sudo group
Assuming your Linux distro has a `sudo` group (like Ubuntu 16.04), simply add the user to
this admin group:
```
sudo usermod -a -G sudo grader
```

## Update all currently installed packages

- Update the package indexes `apt-get update` 

- To actually upgrade the installed packages `apt-get upgrade` 


## Set-up SSH keys for user grader
As root user do:
```
mkdir /home/grader/.ssh
chown grader:grader /home/grader/.ssh
chmod 700 /home/grader/.ssh
cp /root/.ssh/authorized_keys /home/grader/.ssh/
chown grader:grader /home/grader/.ssh/authorized_keys
chmod 644 /home/grader/.ssh/authorized_keys
```

Can now login as the `grader` user using the command:
`ssh grader@18.184.74.60  -p 22 -i ~/.ssh/linuxrsa`


## Disable root login
Change the PasswordAuthentication line to no instead of yes in the file `sudo nano /etc/ssh/sshd_config`:

- So it reads:
```
PasswordAuthentication no
```
Do ` sudo service ssh restart` for the changes to take effect.


## Change timezone to UTC
Check the timezone with the `date` command. This will display the current timezone after the time.
If it's not UTC change it like this:

`sudo timedatectl set-timezone UTC`

## Add SSH port 2200

- Reconfigure the port for the ssh server:
`sudo nano /etc/ssh/sshd_config`
- and add:
`port 2200`

- Then reload the configuration:
`sudo service ssh force-reload`

- and connect via:

`ssh grader@18.184.74.60  -p 2200 -i ~/.ssh/linuxrsa`


## Configuration Uncomplicated Firewall (UFW)
By default, block all incoming connections on all ports:

`sudo ufw default deny incoming`

- Allow outgoing connection on all ports:

`sudo ufw default allow outgoing`

- Allow incoming connection for SSH on port 2200:

`sudo ufw allow 2200/tcp`

- Allow incoming connections for HTTP on port 80:

`sudo ufw allow www`

- Allow incoming connection for NTP on port 123:

`sudo ufw allow ntp`

- To check the rules that have been added before enabling the firewall use:

`sudo ufw show added`

- To enable the firewall, use:

`sudo ufw enable`

- To check the status of the firewall, use:

`sudo ufw status`

## Install Apache to serve a Python mod_wsgi application
- Install Apache:

`sudo apt-get install apache2`

- Install the `libapache2-mod-wsgi` package:

`sudo apt-get install libapache2-mod-wsgi`

## Install and configure the PostgreSQL database system
 ```
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
```
 
## Create a PostgreSQL role named catalog and a database named catalog
```
sudo -u postgres -i
postgres:~$ createuser catalog
postgres:~$ createdb catalog
postgres:~$ psql catalog
postgres=# ALTER DATABASE catalog OWNER TO catalog;
postgres=# ALTER USER catalog WITH PASSWORD 'catalog';
postgres=# \q
postgres:~$ exit

```

## Install Flask, SQLAlchemy, etc
- Issue the following commands:
```
sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqlalchemy python-pip
sudo pip install oauth2client
sudo pip install requests
sudo pip install httplib2
sudo pip install flask-seasurf
```


## Install Git version control software
`sudo apt-get install git`

## Clone the repository that contains Project 4 Catalog app

```
cd /var/www
sudo mkdir catalog
sudo chown -R grader:grader catalog
cd catalog
sudo git clone https://github.com/hassangamal/ItemCatalog catalog
```

## Configure the Catalog app
- Change `app.py` to `__init__.py`
-  Provide the full path (or location) to all the files, instead of just the filename

## Configure Google OAuth
- Add the public IP address of your server to the “Authorized javascript origins”
- Add the public IP address to the client secrets JSON file

## Configure the web application to connect to the PostgreSQL database instead of a SQLite database
 ```
 sudo nano /var/www/catalog/catalog/__inti__.py
 -Change the DATABASE_URI setting in the file from 'sqlite:///database_input.db' to 'postgresql://catalog:catalog@localhost/catalog', and save
``` 

## Configure the Database to connect to the PostgreSQL database instead of a SQLite database
  ```
 sudo nano /var/www/catalog/catalog/Catalog_db.py
 -Change the DATABASE_URI setting in the file from 'sqlite:///Catalog_db.db' to 'postgresql://catalog:catalog@localhost/catalog', and save
 ```
 
 ## Configure Apache to serve the web application using WSGI
- Create the web application WSGI file.
 ```
sudo nano /var/www/catalog/app.wsgi
```
- Add the following lines to the file, and save the file.
```
#!/usr/bin/python
import sys 
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")
from project4 import app as application
application.secret_key = 'super_secret_key'

```
- Update the Apache configuration file to serve the web application with WSGI.
```
sudo nano /etc/apache2/sites-enabled/000-default.conf
```
- Add the following line inside the `<VirtualHost *:80>` element, and save the file.
```
        ServerName 18.184.74.60
	ServerAdmin admin@18.184.74.60
    WSGIScriptAlias / /var/www/catalog/app.wsgi
	<Directory /var/www/catalog/catalog>
		Order allow,deny
		Allow from all
	</Directory>
	Alias /static /var/www//catalog/catalog/static
	<Directory /var/www/catalog/catalog/static/>
		Order allow,deny
		Allow from all
	</Directory>
	ErrorLog ${APACHE_LOG_DIR}/error.log
	LogLevel warn
	CustomLog ${APACHE_LOG_DIR}/access.log combined
 
```

- Restart Apache.
```
sudo apache2ctl restart
```
## References
- [Ask Ubuntu](http://askubuntu.com/)

- [PosgreSQL Docs](https://www.postgresql.org/docs/9.5/static/index.html)

- [Apache Docs](https://httpd.apache.org/docs/2.4/)

-https://github.com/chuanqin3/udacity-linux-configuration

-https://github.com/basmaashouur/full-stack-udacity

-https://github.com/hima-Megahed/Linux-Server-Configuration

-https://github.com/mulligan121/Udacity-Linux-Configuration
