#  Linux-server
<img src="Linux.png" width="50">  Configure Linux Server the turn it to Web Application Server using Apache2

## Project Overview
Taking the baseline installation of a Linux server and prepare it to host your web applications. Secure the server from a number of attack vectors, installing and configuring a database server, and deploy web applications onto it. Learn how to access, secure, and perform the initial configuration of a bare-bones Linux server. learn how to install and configure a web and database server and actually host a web application.




README.md file include all of the following:
i. The IP address and SSH port so your server can be accessed by the reviewer.

ii. The complete URL to your hosted web application.

iii. A summary of software you installed and configuration changes made.

iv. A list of any third-party resources you made use of to complete this project.

Locate the SSH key you created for the grader user.


## Documentation
To add a new user to VM, just substitute the actual username with the username (**username is placeholder in the code**): 
```
$ sudo adduser username
```
To get info about the user:
```
$ finger username
```
To login to VM for the user (username/ localhost IP address/ port/ key):
```
$ssh username@127.0.0.1 -p 2222 -i ~/.ssh/key
```

## VM Up to Date
in order to get newest versions of packages we have to preform the following tasks in order:

1. To downloads the package lists from the repositories and "updates" them to get information on the newest versions of packages and their dependencies:
``` 
$ sudo apt-get update
```
2. To fetch new versions of packages existing on the machine if APT knows about these new versions:
``` 
$ sudo apt-get upgrade
```
3. To remove leftover dependencies of packages you no longer have:
``` 
$ sudo apt-get autoremove
```
4. To download any other packages :
``` 
$ sudo apt-get install packagename
```


## License

This `Linux-server` project is a released under the [MIT License](https://opensource.org/licenses/MIT)
