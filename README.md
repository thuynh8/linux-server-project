# Linux Server Configuration Project
A baseline installation of a Linux distribution on a virtual machine to host a Flask web applications. This project includes installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.  
[![linux-server-project.png](https://s14.postimg.org/kj3t0g0gh/linux_server_project.png)](https://postimg.org/image/5zwnz17bh/)  
**Note:** By the time you are viewing this page, and trying to access the website via the IP link provided, AWS might be no longer active; thus, the website might be inaccessible.  
## Access  
**IP Address:** 35.162.148.243  
**SSH Port:** 2200  
**URL:** http://ec2-35-162-148-243.us-west-2.compute.amazonaws.com  
## Installed Packages
Name | Description
-----|------------
apache2 | Web Server
libapache2-mod-wsgi | Tool that serves Python web apps from Apache server
finger | User information look up program
postgresql | Relational database system
sqlalchemy | SQL toolkit and ORM for Python
flask | Python web framework
git | Version control tool  
## Configuration Summary
1. Create a new user  
  - Create a new user with name 'grader':   
    `$ sudo adduser grader`  
  - Grant user permission to perform sudo command:  
    `$ sudo nano /etc/sudoers.d/grader`  
    `$ grader ALL=(ALL) NOPASSWD:ALL`  
  - Generate a SSH key pair on the local machine:  
    `$ ssh-keygen`  
  - Place the public key on remote server:  
    `$ mkdir .ssh`  
    `$ touch .ssh/authorized_keys`, follows by pasting public key into authorized_keys file   
    `$ chmod 700 .ssh`   
    `$ chmod 644 .ssh/authorized_keys`  
  - Login to the server using ssh key:  
    `$ ssh grader@35.162.148.243 -p PORT_NUMBER -i ~/.ssh/authorized_keys`
2. Update all currently installed packages
  - Get a list of packages to be upgraded:  
    `$ sudo apt-get update`  
  - Upgrade packages to newer versions:  
    `$ sudo apt-get upgrade`  
3. Change the SSH port from 22 to 2200  
  - Open the config file:  
    `$ sudo nano /etc/ssh/sshd_config`  
  - Change to Port 2200  
  - Also change `PermitRootLogin` to `no` to disable remote login of the root user  
4. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)  
  - Block all incomming requests:  
    `$ sudo ufw default deny incoming`  
  - Allow all outgoing requests from the server:  
    `$ sudo ufw default allow outgoing`  
  - Allow incoming connections for SSH (port 2200):  
    `$ sudo ufw allow 2200/tcp`  
  - Allow incoming connections for HTTP (port 80):  
    `$ sudo ufw allow www`  
  - Allow incoming connections for NTP (port 123):  
    `$ sudo ufw allow ntp`  
5. Configure the local timezone to UTC  
  - `$ sudo dpkg-reconfigure tzdata`  
  - Select UTC  
6. Install and configure Apache to serve a Python mod_wsgi application  
  - Install Apache2:  
    `$ sudo apt-get install apache2`  
  - Install mod_wsgi for serving Python apps from Apache:  
    `$ sudo apt-get install python-setuptools libapache2-mod-wsgi`  
  - Restart the Apache server for mod_wsgi to load:  
    `$ sudo service apache2 restart`  
7. Install Git and clone Catalog app
8. Install and configure PostgreSQL  
  - Install PostgreSQL:  
    `$ sudo apt-get install postgresql postgresql-contrib`  
  - Check that no remote connections are allowed (default):  
    `$ sudo vim /etc/postgresql/9.3/main/pg_hba.conf`  
  - Open the database setup file:  
    `$ sudo vim database_setup.py`  
  - Change from sqlite to postgresql:  
    `python engine = create_engine('postgresql://catalog:PW-FOR-DB@localhost/catalog')`  
  - Rename application.py:  
    `$ mv application.py __init__.py`  
  - Create linux user for psql:  
    `$ sudo adduser catalog`  
  - Change to default user postgres:  
    `$ sudo su - postgre`  
  - Connect to the system:  
    `$ psql`  
  - Create user with LOGIN role and set a password:  
    `CREATE USER catalog WITH PASSWORD 'PW-FOR-DB';`  
  - Allow the user to create database tables:  
    `ALTER USER catalog CREATEDB;`  
  - Create database:  
    `CREATE DATABASE catalog WITH OWNER catalog;`  
  - Connect to the database catalog:  
    `\c catalog` 
  - Revoke all rights:  
    `REVOKE ALL ON SCHEMA public FROM public;`  
  - Grant only access to the catalog role:  
    `GRANT ALL ON SCHEMA public TO catalog;`  
  - Create postgreSQL database schema:  
    `$ python database_setup.py`  
9. Run the application
  - Restart Apache2 server  
    `$ sudo service apache2 restart`  
  - Go to this link: http://ec2-35-162-148-243.us-west-2.compute.amazonaws.com/  
  
## Third-party Resources Used to Complete This Project  
- https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps142  
- http://askubuntu.com/questions/59458/error-message-when-i-run-sudo-unable-to-resolve-host-none  
- https://discussions.udacity.com/t/configuring-utc-time-zone/35776  
- https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04  
- https://discussions.udacity.com/t/markedly-underwhelming-and-potentially-wrong-resource-list-for-p5/8587  
- https://discussions.udacity.com/t/project-5-resources/28343


