#  Linux Server Configuration
<img src="img/Linux.png" width="50">  Configure Linux Server the turn it to Web Application Server using Apache2

## Project Overview
Taking the baseline installation of a Linux server and prepare it to a web applications. Secure the server from a number of attack vectors, installing and configuring a database server, and deploy web applications onto it. Access, secure, and perform the initial configuration of a bare-bones Linux server. Install and configure a web and database server and host a web application.

## Server Details

IP address from Amazon EC2: 13.59.231.249

SSH port: 2200

URL: http://ec2-13-59-231-249.us-east-2.compute.amazonaws.com     

##Step by step process

### 1.  Update or Installed Packages
In order to get newest versions of packages we have to preform the following tasks in order:

1. `$ sudo apt-get update` to downloads the package lists from the repositories and "updates" them to get information on the newest versions of packages and their dependencies.
2. `$ sudo apt-get upgrade` to fetch new versions of packages existing on the machine if APT knows about these new versions.
3. `$ sudo apt-get autoremove` to remove leftover dependencies of packages you no longer have.
4. `$ sudo apt-get install packagename` to download any other packages.

### 2. Change the SSH port from 22 to 2200
Edit the file `$ nano /etc/ssh/sshd_config` and change **Port 22** to **Port 2200**
```diff
# what ports, IPs and protocols we listen for
- Port 22
+ Port 2200
```
Then restart the SSH service:
```
$ sudo service ssh restart
```
Will now change the command to login to the server:
```
$ ssh username@127.0.0.1 -p 2200 -i ~/.ssh/key
```

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

### 4. New User Configuration
1. Log into Amazon EC2 instance: `$ ssh ubuntu@13.59.231.249`
2. Add a new user **grader**: `$ sudo adduser grader`
3. Create a new file under the suoders directory: `$ sudo nano /etc/sudoers.d/grader` 
4. Fill that file with the following line of text: `grader ALL=(ALL:ALL) ALL` and save it.
5. to get info about the user `$ finger username` 
- For more info [click here](https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-ubuntu-16-04)

#### - key authentication for grader user
1. Generate an encryption key on your local machine with: `$ ssh-keygen -f ~/.ssh/udacity_key.rsa`
2. Log into the remote VM as root user through ssh and create the following file:` $ touch /home/grader/.ssh/authorized_keys.`
3. Copy the content of the udacity_key.pub file from your local machine to the `/home/grader/.ssh/authorized_keys` file you just created on the remote VM. Then change some permissions:
`$ sudo chmod 700 /home/grader/.ssh`
` sudo chmod 644 /home/grader/.ssh/authorized_keys`
Finally change the owner from root to grader: `$ sudo chown -R grader:grader /home/grader/.ssh`
Now you are able to log into the remote VM through ssh with the following command: 
```
$ ssh -i ~/.ssh/udacity_key.rsa grader@13.59.231.249
```

#### - Enforce key-based authentication
1. `$ sudo nano /etc/ssh/sshd_config`  Find the PasswordAuthentication line and edit it to `no`.
2. Restart service `$ sudo service ssh restart`

### 5. Timezone UTC
To check the current timezone use the following command `$ date`. In case it's not UTC, here is the command to do so:
```
$ sudo timedatectl set-timezone UTC
```
or open time configuration dialog and set it to UTC with:
```
sudo dpkg-reconfigure tzdata
```
- For more info [click here](https://www.digitalocean.com/community/tutorials/how-to-set-up-timezone-and-ntp-synchronization-on-ubuntu-14-04-quickstart)

### 6. Install and configure Apache to serve a Python mod_wsgi application

1. Install Apache: `$ sudo apt-get install apache2`
2. Install *mod_wsgi*: `$ sudo apt-get install libapache2-mod-wsgi python-dev`
3. Enable mod_wsgi: `$ sudo a2enmod wsgi`
4. Start Apache service`$ sudo service apache2 start`

### 7. Install and configure PostgreSQL
Install PostgreSQL with:
```
sudo apt-get install postgresql postgresql-contrib
```
To ensure that remote connections to PostgreSQL are not allowed, I checked that the configuration file `/etc/postgresql/9.3/main/pg_hba.conf` only allowed connections from the local host addresses `127.0.0.1` for `IPv4` and `::1` for `IPv6`.

Create a PostgreSQL user called catalog with:
```
sudo -u postgres createuser -P catalog
```
You are prompted for a password. This creates a normal user that can't create databases, roles (users).

Create an empty database called catalog with:
```
sudo -u postgres createdb -O catalog catalog
```
For more info [Ubuntu documentation PostgreSQL](https://help.ubuntu.com/community/PostgreSQL)

### 8. Install Git
 Install Git `sudo apt-get install git`
#### Clone the Catalog app from Github
 - To clone Restaurants_Catalog project from my github `git clone https://github.com/Nshmais/Restaurants_Catalog`
 - Make a catalog.wsgi file to serve the application over the mod_wsgi. That file should look like this:
 ```
    catalog.wsgi
    <BEGIN>
    import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0, "/var/www/catalog/")

    from Start import app as application
    <END>
 ```

### 9. Install Flask, SQLAlchemy, etc
Issue the following commands:
```
sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqlalchemy python-pip
sudo pip install oauth2client
sudo pip install requests
sudo pip install httplib2
sudo pip install flask-seasurf
```




## License
This `Linux-server` project is a released under the [MIT License](https://opensource.org/licenses/MIT)
