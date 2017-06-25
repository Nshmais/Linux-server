#  Linux Server Configuration
<img src="img/Linux.png" width="50">  Configure Linux Server the turn it to Web Application Server using Apache2

## Project Overview
Taking the baseline installation of a Linux server and prepare it to a web applications. Secure the server from a number of attack vectors, installing and configuring a database server, and deploy web applications onto it. Access, secure, and perform the initial configuration of a bare-bones Linux server. Install and configure a web and database server and host a web application.

## Server Details

IP address from Amazon EC2: 13.59.231.249

SSH port: 2200

URL: http://ec2-13-59-231-249.us-east-2.compute.amazonaws.com     

## 🤖 Step by step process
Create an instance in Amazon EC2. Log into Amazon EC2 instance with your IP adress and key if setup, now the follow these steps will guide to deploy the app to the server.

### 1.  Update or Installed Packages
In order to get newest versions of packages we have to preform the following tasks in order:
1. `$ sudo apt-get update` to downloads the package lists from the repositories and "updates" them to get information on the newest versions of packages and their dependencies.
2. `$ sudo apt-get upgrade` to fetch new versions of packages existing on the machine if APT knows about these new versions.
3. `$ sudo apt-get autoremove` to remove leftover dependencies of packages you no longer have.
4. `$ sudo apt-get install packagename` to download any other packages.

### 2. Change the SSH port from 22 to 2200
1. Edit the file `$ nano /etc/ssh/sshd_config` and change **Port 22** to **Port 2200**
```diff
# what ports, IPs and protocols we listen for
- Port 22
+ Port 2200
```
2. Then restart the SSH service:`$ sudo service ssh restart`
- Will now change the command to login to the server:`$ ssh grader@ec2-13-59-231-249.us-east-2.compute.amazonaws.com -p 2200 -i amazon2.pem`

### 3. Configure the Uncomplicated Firewall (UFW) 
1. By default, block all incoming connections on all ports: `$ sudo ufw default deny incoming`
2. Allow outgoing connection on all ports: `$ sudo ufw default allow outgoing`
3. Allow incoming connection for SSH on port 2200: `$ sudo ufw allow 2200/tcp`
4. Allow incoming connections for HTTP on port 80: `$ sudo ufw allow www`
5. Allow incoming connection for NTP on port 123: `$ sudo ufw allow ntp`
6. To check the rules that have been added before enabling the firewall use: `$ sudo ufw show added`
7. To enable the firewall, use: `$ sudo ufw enable`
8. To check the status of the firewall, use: `$ sudo ufw status`
- The firewall should be active. For more info [click here](https://help.ubuntu.com/community/UFW)

### 4. New User Configuration (grader)
1. Add a new user **grader**: `$ sudo adduser grader`
2. Create a new file under the suoders directory: `$ sudo nano /etc/sudoers.d/grader` 
3. Fill that file with the following line of text: `grader ALL=(ALL:ALL) ALL` and save it.
4. to get info about the user `$ finger username` 
- For more info [click here](https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-ubuntu-16-04)

#### key authentication for grader user
1. Generate an encryption key on your local machine with: `$ ssh-keygen -f ~/.ssh/udacity_key.rsa`
2. Log into the remote VM as root user through ssh and create the following file:` $ touch /home/grader/.ssh/authorized_keys.`
3. Copy the content of the udacity_key.pub file from your local machine to the `/home/grader/.ssh/authorized_keys` file you just created on the remote VM. Then change some permissions:
`$ sudo chmod 700 /home/grader/.ssh`
`$ sudo chmod 644 /home/grader/.ssh/authorized_keys`
Finally change the owner from root to grader: `$ sudo chown -R grader:grader /home/grader/.ssh`
Now you are able to log into the remote VM through ssh 
**Or**
if create your key from amazon just download it and log in 
```
   ssh grader@ec2-13-59-231-249.us-east-2.compute.amazonaws.com -i amazon2.pem
```

#### Enforce key-based authentication
1. `$ sudo nano /etc/ssh/sshd_config`  Find the *PasswordAuthentication* line and edit it to `no`.
2. Restart service `$ sudo service ssh restart`

#### Disable ssh login for *root* user
1. `$ sudo nano /etc/ssh/sshd_config`. Find the *PermitRootLogin* line and edit it to `no`.
2. Restart service `$ sudo service ssh restart`


### 5. Timezone UTC
To check the current timezone use the following command `$ date`. In case it's not UTC, here is the command to do so:
`$ sudo timedatectl set-timezone UTC`

**or** open time configuration dialog and set it to UTC with:`$ sudo dpkg-reconfigure tzdata`
- For more info [click here](https://www.digitalocean.com/community/tutorials/how-to-set-up-timezone-and-ntp-synchronization-on-ubuntu-14-04-quickstart)

### 6. Install and configure Apache to serve a Python mod_wsgi application
1. Install Apache: `$ sudo apt-get install apache2`
2. Install *mod_wsgi*: `$ sudo apt-get install libapache2-mod-wsgi python-dev`
3. Enable mod_wsgi: `$ sudo a2enmod wsgi`
4. Start Apache service`$ sudo service apache2 start`

### 7. Install Git
 Install Git `sudo apt-get install git`
#### Clone the Catalog app from Github
 - To clone Restaurants_Catalog project from my github `git clone https://github.com/Nshmais/Restaurants_Catalog`
 - Make a catalog.wsgi file to serve the application over the mod_wsgi. That file should look like this:
 ```python
    import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0, "/var/www/catalog/")
    
    from Start import app as application
 ```
### 8. Install virtual environment, Flask and SQLAlchemy, etc
1. Install pip, the tool for installing Python packages: `$ sudo apt-get install python-pip`
2. If virtualenv is not installed, use pip to install it using the following command: `$ sudo pip install virtualenv`
3. Move to the catalog folder: $ cd /var/www/catalog. Then create a new virtual environment with the following command: 
`$ sudo virtualenv venv`
4. Activate the virtual environment: `$ source venv/bin/activate`
5. Install Flask: `$ pip install Flask bleach httplib2 request oauth2client sqlalchemy`
6. Install all the other project's dependencies: `$ pip install psycopg2`

### 9. Configure and enable a new virtual host
1. Create a virtual host conifg file: `$ sudo nano /etc/apache2/sites-available/catalog.conf`
2. Paste in the following lines of code:
```
<VirtualHost *:80>
    ServerName 13.59.231.249
    ServerAlias http://ec2-13-59-231-249.us-east-2.compute.amazonaws.com
    ServerAdmin admin@13.59.231.249
    WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
    WSGIProcessGroup catalog
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalog/template
    <Directory /var/www/catalog/template>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
- The WSGIDaemonProcess line specifies what Python to use and can save you from a big headache. In this case we are explicitly saying to use the virtual environment and its packages to run the application.
3. Enable the new virtual host: `$ sudo a2ensite catalog`
- for more info [click here](https://www.digitalocean.com/community/tutorials/how-to-run-django-with-mod_wsgi-and-apache-with-a-virtualenv-python-environment-on-a-debian-vps)

### 10. Install and configure PostgreSQL
1. Install some necessary Python packages for working with PostgreSQL: `$ sudo apt-get install libpq-dev python-dev`.
2. Install PostgreSQL: `$ sudo apt-get install postgresql postgresql-contrib`.
3. Postgres is automatically creating a new user during its installation, whose name is 'postgres'. That is a tusted user who can access the database software. So let's change the user with: `$ sudo su - postgres`, then connect to the database system with `$ psql`.
4. Create a new user called 'catalog' with his password: `# CREATE USER catalog WITH PASSWORD 'udacitypassword';`.
5. Give *catalog* user the CREATEDB capability: `# ALTER USER catalog CREATEDB;`.
6. Create the 'catalog' database owned by *catalog* user: `# CREATE DATABASE catalog WITH OWNER catalog;`.
7. Connect to the database: `# \c catalog`.
8. Revoke all rights: `# REVOKE ALL ON SCHEMA public FROM public;`.
9. Lock down the permissions to only let *catalog* role create tables: `# GRANT ALL ON SCHEMA public TO catalog;`.
10. Log out from PostgreSQL: `# \q`. Then return to the *grader* user: `$ exit`.
11. Inside the `Start.py`, `lotsofmenus.py`, and `database_setup.py`, the database connection showld be updated with: 
```python
engine = create_engine('postgresql://catalog:udacitypassword@localhost/catalog')
```
12. Setup the database with: `$ python /var/www/catalog/catalog/setup_database.py`.
13. To prevent potential attacks from the outer world we double check that no remote connections to the database are allowed. Open the following file: `$ sudo nano /etc/postgresql/9.3/main/pg_hba.conf` and edit it, if necessary, to make it look like this: 
```
local   all             postgres                                peer
local   all             all                                     peer
host    all             all             127.0.0.1/32            md5
host    all             all             ::1/128                 md5
```
Source: [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps).


### 11. Update OAuth authorized JavaScript origins
1. To let users correctly log-in change the authorized URI to [http://ec2-52-34-208-247.us-west-2.compute.amazonaws.com/](http://ec2-52-34-208-247.us-west-2.compute.amazonaws.com/) on both Google and Facebook developer dashboards.

### 12. Restart Apache to launch the app
 `$ sudo service apache2 restart`.

## License
This `Linux-server` project is a released under the [MIT License](https://opensource.org/licenses/MIT)
