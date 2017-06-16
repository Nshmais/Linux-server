#  Linux Server Configuration
<img src="Linux.png" width="50">  Configure Linux Server the turn it to Web Application Server using Apache2

## Project Overview
Taking the baseline installation of a Linux server and prepare it to a web applications. Secure the server from a number of attack vectors, installing and configuring a database server, and deploy web applications onto it. Access, secure, and perform the initial configuration of a bare-bones Linux server. Install and configure a web and database server and host a web application.

## Server Details

IP address from Amazon Lightsail: 

SSH port: 2200

URL: 


~~README.md file include all of the following:
i. The IP address and SSH port so your server can be accessed by the reviewer.
ii. The complete URL to your hosted web application.
iii. A summary of software you installed and configuration changes made.
iv. A list of any third-party resources you made use of to complete this project.
Locate the SSH key you created for the grader user.~~


## Update or Installed Packages
In order to get newest versions of packages we have to preform the following tasks in order:

1. `$ sudo apt-get update` to downloads the package lists from the repositories and "updates" them to get information on the newest versions of packages and their dependencies.
2. `$ sudo apt-get upgrade` to fetch new versions of packages existing on the machine if APT knows about these new versions.
3. `$ sudo apt-get autoremove` to remove leftover dependencies of packages you no longer have.
4. `$ sudo apt-get install packagename` to download any other packages.

## Change the SSH port from 22 to 2200
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
$ssh username@127.0.0.1 -p 2200 -i ~/.ssh/key
```

## Configure the Uncomplicated Firewall (UFW) 
By default, block all incoming connections on all ports:
```
sudo ufw default deny incoming
```
Allow outgoing connection on all ports:
```
sudo ufw default allow outgoing
```
Allow incoming connection for SSH on port 2200:
```
sudo ufw allow 2200/tcp
```
Allow incoming connections for HTTP on port 80:
```
sudo ufw allow www
```
Allow incoming connection for NTP on port 123:
```
sudo ufw allow ntp
```
To check the rules that have been added before enabling the firewall use:
```
sudo ufw show added
```
To enable the firewall, use:
```
sudo ufw enable
```
To check the status of the firewall, use:
```
sudo ufw status
```
The firewall should be active. For more info [click here](https://help.ubuntu.com/community/UFW)

## New User Configuration
- `$ sudo adduser username` to add a new user to VM, just substitute the actual username with the username (**username is placeholder in the code**).
- `$ finger username` to get info about the user.
- `$ usermod -aG sudo grader` add the user to superuser (sudo).
- `$ssh username@127.0.0.1 -p 2222 -i ~/.ssh/key` to login to VM for the user (username/ localhost IP address/ port/ key).
- For more info [click here](https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-ubuntu-16-04)

## Timezone UTC
To check the current timezone use the following command `$ date`. In case it's not UTC, here is the command to do so:
```
$ sudo timedatectl set-timezone UTC
```
For more info [click here](https://www.digitalocean.com/community/tutorials/how-to-set-up-timezone-and-ntp-synchronization-on-ubuntu-14-04-quickstart)

## Install and configure Apache to serve a Python mod_wsgi application

Install Apache:
```
sudo apt-get install apache2
```
Install the libapache2-mod-wsgi package:
```
sudo apt-get install libapache2-mod-wsgi
```
~~Edit the `nano /etc/apache2/sites-enabled/000-default.conf` file, add the following line at the end of the `<VirtualHost *:80>` block, right before the closing `</VirtualHost>` line: `WSGIScriptAlias / /var/www/html/myapp.wsgi`~~

## Install and configure PostgreSQL
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

## Install Git
`sudo apt-get install git`
To clone Restaurants_Catalog project from my github
`git clone https://github.com/Nshmais/Restaurants_Catalog`

## Install Flask, SQLAlchemy, etc
Issue the following commands:
```
sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqlalchemy python-pip
sudo pip install oauth2client
sudo pip install requests
sudo pip install httplib2
sudo pip install flask-seasurf
```
## Amazon Lightsail
##### 1. Log in!
  First, log in to Lightsail. If you don't already have an Amazon Web Services account, you'll be prompted to create one.

##### 2. Create an instance.
  Once you're logged in, Lightsail will give you a friendly message with a robot on it, prompting you to create an instance. A Lightsail instance is a Linux server running on a virtual machine inside an Amazon datacenter.

##### 3. Choose an instance image: Ubuntu
  Lightsail supports a lot of different instance types. An instance image is a particular software setup, including an operating system and optionally built-in applications. For this project, you'll want a plain Ubuntu Linux image. There are two settings to make here:
  A. First, choose "OS Only" (rather than "Apps + OS"). 
  B. Second, choose Ubuntu as the operating system.

##### 4. Choose your instance plan.
  The instance plan controls how powerful of a server you get. It also controls how much money they want to charge you. For this project, $5 (first month free) instance is just fine.
  
##### 5. Give your instance a hostname.
  Every instance needs a unique hostname. You can use any name you like, as long as it doesn't have spaces or unusual characters in it. Your instance's name will be visible to you and to the project reviewer.

##### 6. Wait for it to start up.
  It may take a few seconds/minutes for your instance to start up.

##### 7. Running instance
  Once your instance has started up, you can log into it with SSH from computer.`ssh Ubuntu@()ip_address` you can leave the port part off if its defult 22 or if you change it you have to mention it `ssh Ubuntu@()ip_address -p port_number`

The public IP address of the instance is displayed along with its name. In the above picture it's 54.84.49.254. The DNS name of this instance is ec2-54-84-49-254.compute-1.amazonaws.com.


## License
This `Linux-server` project is a released under the [MIT License](https://opensource.org/licenses/MIT)
