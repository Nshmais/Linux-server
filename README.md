#  Linux-server
<img src="Linux.png" width="50">  Configure Linux Server the turn it to Web Application Server using Apache2

## Project Overview
Taking the baseline installation of a Linux server and prepare it to host your web applications. Secure the server from a number of attack vectors, installing and configuring a database server, and deploy web applications onto it. Learn how to access, secure, and perform the initial configuration of a bare-bones Linux server. learn how to install and configure a web and database server and actually host a web application.

## Server Details

IP address: 

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


## New User Configuration
- `$ sudo adduser username` to add a new user to VM, just substitute the actual username with the username (**username is placeholder in the code**).
- `$ finger username` to get info about the user.
- `$ usermod -aG sudo grader` add the user to superuser (sudo).
- `$ssh username@127.0.0.1 -p 2222 -i ~/.ssh/key` to login to VM for the user (username/ localhost IP address/ port/ key).

## Timezone UTC

To check the current timezone use the following command `$ date`. In case it's not UTC, here is the command to do so:
```
$ sudo timedatectl set-timezone UTC
```

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
To ensure that remote connections to PostgreSQL are not allowed, I checked that the configuration file /etc/postgresql/9.3/main/pg_hba.conf only allowed connections from the local host addresses 127.0.0.1 for IPv4 and ::1 for IPv6.

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

## License
This `Linux-server` project is a released under the [MIT License](https://opensource.org/licenses/MIT)
