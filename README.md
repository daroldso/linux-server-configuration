Linux Server Configuration
=============
This is the Project 5 of Full Stack Web Developer Nanodegree by Udacity.

IP address: `52.10.138.65`

Port: `2200`

URL: [http://ec2-52-10-138-65.us-west-2.compute.amazonaws.com](http://ec2-52-10-138-65.us-west-2.compute.amazonaws.com)

## Software installed
### System (apt-get)
- finger
- apache2
- postgresql
- postgresql-contrib
- git
- python-pip
- libpq-dev (for psycopg2)
- python-dev (for psycopg2)

### Python modules (pip)
- virtualenv
- Flask
- sqlalchemy
- psycopg2
- oauth2client
- werkzeug==0.8.3
- flask==0.9
- Flask-Login==0.1.3

## Configuration made
### Update and Upgrade
```
sudo apt-get update 
sudo apt-get upgrade
```

### Create new user `grader`
```
adduser grader
usermod -aG sudo grader
```

### Use key authentication
In local, generate key pair
```
ssh-keygen
```

Install the public key on server. Login as the user you want to use key auth
```
mkdir .ssh
touch .ssh/authorized_keys
```

Add one key per line into authorized_keys
```
sudo nano .ssh/authorized_keys
```

Change ssh folder file permission
```
chmod 700 .ssh
chmod 644 .ssh/authorized_keys
```

### Disable password authentication
```
sudo nano /etc/ssh/sshd_config
```
Change `PasswordAuthentication yes` to `PasswordAuthentication no`

### Disable remote root login
```
sudo nano /etc/ssh/sshd_config
```
Change `PermitRootLogin without-password` to `PermitRootLogin no`

### Change timezone to UTC
```
dpkg-reconfigure tzdata
```
Select `Etc` and `UTC`

### Change ssh port from 22 to 2200
```
sudo nano /etc/ssh/sshd_config
```
Change `Port 22` to `Port 2200`

### Allow SSH, HTTP and NTP
```
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/udp
sudo ufw enable
```

## Install and configure Apache to serve a Python mod_wsgi application
Install Apache
```
sudo apt-get install apache2
```

Install and enable mod_wsgi
```
sudo apt-get install libapache2-mod-wsgi
sudo a2enmod wsgi
```
Add `myapp.wsgi`
```
touch /var/www/html/myapp.wsgi
```
Edit `/etc/apache2/sites-enabled/000-default.conf` and add
```
WSGIScriptAlias / /var/www/html/myapp.wsgi
```

## Install and configure PostgreSQL
 1. Install PostgreSQL
 1. Login `psql` as `postgres`
 1. Add user `catalog` by `CREATE USER catalog WITH PASSWORD 'somepassword';`
 1. Create the `itemcatalog` database owned by `catalog` user: `CREATE DATABASE itemcatalog WITH OWNER catalog;`.
 1. Connect to the database: `\c itemcatalog`.
 1. Revoke all rights from public: `REVOKE ALL ON SCHEMA public FROM public;`.
 1. Lock down the permissions to only let catalog role perform CRUD action on tables: `GRANT ALL ON SCHEMA public TO catalog;`.

Inside the Flask application, connection string is changed to 
```
engine = create_engine('postgresql://catalog:somepassword@localhost/itemcatalog')
```

To prevent remote connection, we need to edit `sudo nano /etc/postgresql/9.1/main/pg_hba.conf` and ensure below lines are not commented out.
 ```
local   all             postgres                                peer
local   all             all                                     peer
host    all             all             127.0.0.1/32            md5
host    all             all             ::1/128                 md5
 ```

## Install git, clone and set up your Catalog App project
Install git, python-dev, python-pip
```
sudo apt-get install git, python-dev, python-pip
```

Clone the Item Catalog project from git and copy necessary files to `var/www/html`
```
git clone https://github.com/daroldso/fullstack-nanodegree-vm.git
sudo cp -r ~/vagrant/catalog/* /var/www/html
```

Change files owner to grader
```
sudo chown -R grader:grader /var/www/html/*
```

Install virtual environment to keep the applications isolated from main system.
```
sudo pip install virtualenv
```

Activate virtual environment
```
sudo virtualenv venv
source venv/bin/activate 
```

Install Flask inside virtual environment
```
sudo pip install Flask
```

Edit `sudo nano /etc/apache2/sites-available/000-default.conf` and add below code inside `<VirtualHost *:80></VirtualHost>`
```
WSGIScriptAlias / /var/www/html/myapp.wsgi

<Directory /var/www/html/>
    Order allow,deny
    Allow from all
</Directory>
Alias /static /var/www/html/static
<Directory /var/www/html/static/>
    Order allow,deny
    Allow from all
</Directory>
ErrorLog ${APACHE_LOG_DIR}/error.log
LogLevel warn
CustomLog ${APACHE_LOG_DIR}/access.log combined
```

Modify `myapp.wsgi` to add below lines
```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/html/")

from main import app as application
application.secret_key = 'super_secret_key'
```

Restart Apache
```
sudo service restart Apache
```

## Resources I made use of
 - [How to secure PostgreSQL on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)
 - [How to deploy a flask application on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)

