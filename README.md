Linux Server Configuration
=============
This is the Project 5 of Full Stack Web Developer Nanodegree by Udacity.

IP address: `52.10.138.65`
Port: `2200`
URL: [http://ec2-52-10-138-65.us-west-2.compute.amazonaws.com](http://ec2-52-10-138-65.us-west-2.compute.amazonaws.com)

A summary of software you installed and configuration changes made.
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
in local, generate key pair
`ssh-keygen`

Install the public key on server. Login as the user you want to use key auth
`mkdir .ssh`
`touch .ssh/authorized_keys`

Add one key per line into authorized_keys
`sudo nano .ssh/authorized_keys`

Change ssh folder file permission
`chmod 700 .ssh`
`chmod 644 .ssh/authorized_keys`

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

### Install and configure Apache to serve a Python mod_wsgi application

### Install and configure PostgreSQL

### Install git, clone and set up your Catalog App project

## Resources I made use of
 - [How to secure PostgreSQL on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)
 - [How to deploy a flask application on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)

