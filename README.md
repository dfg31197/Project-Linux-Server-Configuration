# Project : Linux Server Configuration
- - -
## Credentials
    SSH Port: 2200
    Public IP : 54.237.211.129
# [Click here to see the Live version of the web app!](http://ec2-54-237-211-129.compute-1.amazonaws.com/restaurants)
## Linux Server Configuration Steps :
#### Setting up your SSH
* Create an instance of Amazon's Lightsail
* Download the `PRIVATE_KEY` and note down the `PUBLIC_IP`
* Place the downloaded `PRIVATE_KEY` into your prefered location
* **Add Permissions** : `chmod 400 PRIVATE_KEY`
* **SSH** into your *instance* : `ssh -i /path-to-key/PRIVATE_KEY ubuntu@PUBLIC_IP`
#### Add User "grader"
* Run `sudo adduser grader`
* Enter your credentials
* **Make grader a *sudoer***: Run `sudo nano /etc/sudoers.d/grader` and add line `grader ALL=(ALL) NOPASSWD:ALL`
#### Setup SSH Key-Pair Login for *grader*
* Copy the contents of `ubuntu's public key` by running command `sudo cat .ssh/autherized_key`
* Log in to `grader` using password only once for adding `public key` .
* Log in by running command `su grader` and enter your password.
* Create directory **.ssh** and a file **.ssh/autherized_key**  by running commands:
    * `mkdir .ssh`
    * `touch .ssh/autherized_key`
* Copy the contents of `ubuntu's public key` into `.ssh/authorized_key` by running command:
    * `sudo nano .ssh/autherized_key`
    * Paste the contents and **save**.
* **Set Permissions for `autherized_key`** by running command:
    * `chmod 400 .ssh/autherized_key`
#### Disable Password authentication | Disable login from root user | Reconfigure Port 22
* Edit the file `/etc/ssh/sshd_config`
    * `sudo nano /etc/ssh/sshd_config`
##### Disable Password:
* Find the line **PasswordAuthentication** and change its value to `no`
##### Disable login from root:
* Find the line **PermitRootLogin** and change its value to `no`
*Reference : Udacity Discussion Forums*
##### Reconfigure Port 22
* Find the line **Port** and change its value to `2200`
#### Configure timezone to UTC:
* Run command `sudo timedatectl set-timezone UTC`
*Reference: UbuntuTime*

#### Set up the UFW Firewall
    Login to grader now via port 2200 with command : `ssh -i PRIVATE_KEY grader@PUBLIC_IP -p 2200`
**`Do these steps carefully as a misstep here may lock you out after enabling the firewall`**
* `sudo ufw default deny incoming`
* `sudo ufw default allow outgoing`
* `sudo ufw allow 2200/tcp`
* `sudo ufw allow www`
* `sudo ufw allow ntp`
* **`sudo ufw enable`**
#### Update and Upgrade Packages
* Update :  `sudo apt-get update`
* Upgrade : `sudo apt-get upgrade`
## Install Softwares and Dependencies:
#### Install Git
* `sudo apt-get install git`
#### Install Pip , Flask and other modules
* **PIP** : `sudo apt-get install python-pip`
* **Flask** : `sudo pip install Flask`
*  **Other modules used in the Item Catalogue Project** : 
`sudo pip install sqlalchemy oauth2client twilio psycopg2 httplib2 sqlalchemy_utils python-bleach requests`

#### Install Apache2
* `sudo apt-get install apache2`
#### Install mod wsgi
* `sudo apt-get install libapache2-mod-wsgi`
#### Install PostgreSQL
* PostgreSQL : `sudo apt-get install postgresql postgresql-contrib`
* Python Dependencies : `sudo apt-get install libpq-dev python-dev`
## Clone the Item Catalogue Project
* Create a folder *catalog* in /var/www/catalog:
`sudo mkdir /var/www/catalog`
* Change ownership of *catalog* folder to grader
    * sudo chown grader /var/www/catalog
    * sudo chgrp grader /var/www/catalog
* Clone the **item catalog project** into **catalog** folder
    * `git clone https://github.com/dfg31197/SQLite-plus-Python-CRUD-Web-App.git /var/www/catalog`
* Checkout the **deployment** branch
    *`git checkout deployment`

## Configure PostgreSQL
* Login as default user
    * `sudo su - postgres`
* While as *pstgres@ip-xxxxxx* , get into PostgreSQL Shell
    * `psql`
* Create a **user** named **catalog** and set password
    * `CREATE USER catalog WITH PASSWORD 'dfg123abc';`
* Create a **database** named **catalog**
    * `CREATE DATABASE catalog WITH OWNER catalog`
* Connect to database catalog
    * `\c catalog`
* **Revoke** all rights
    * `REVOKE ALL ON SCHEMA public from public;` 
* **Grant** all rights to user **catalog** 
    * `GRANT ALL ON SCHEMA public TO catalog;`
* Logout from PostgreSQL Shell
    * `\q`
* Logout from default postgresql user terminal
    * `logout`
* Edit file *database_setup.py* at /var/www/catalog/
    * `sudo nano /var/www/catalog/database_setup.py`
* Find line `engine = create_engine("sqlite:///restaurantmenu.db")` and change it to `engine = create_engine("postgresql://catalog:dfg123abc@localhost/catalog")`
* Repeat the step for files `queries.py` and `lotsofmenus.py`
## Set up the Virual Server to serve content
#### Set up mod-wsgi for the app
* **Create and edit file** `dfg_catalog`
    * `sudo nano /var/www/catalog/dfg_catalog`
    * Add :
    * ` import sys`
`import logging`
`logging.basicConfig(stream=sys.stderr)`
`sys.path.insert(0, "/var/www/catalog/")`
`from webserver import app as application`
Reference : Stack Overflow , Udacity Forums*


* **Edit file** `000-default.conf` located at `etc/apache2/sites-available/`
    * `sudo nano /etc/apache2/sites-available/000-default.conf`
    * Add :
    `<VirtualHost *:80>`
    `ServerAdmin sagar.choudhary96@gmail.com`
    `WSGIScriptAlias / /var/www/catalog/bookCatalog.wsgi`
    `<Directory /var/www/catalog/>`
      `Order allow,deny`
      `Allow from all`
    `</Directory>`
    `WSGIScriptAlias /static /var/www/catalog/static`
    `<Directory /var/www/catalog/static/>`
      `Order allow,deny`
      `Allow from all`
    `</Directory>`
    `</VirtualHost>`
*Reference : [Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-set-up-apache-virtual-hosts-on-ubuntu-14-04-lts), Stack Overflow*
### Retrieve Domain name
* Copy your **PUBLIC_IP** and paste in [here](http://www.nmonitoring.com/ip-to-domain-name.html)
* Note down the domain name generated by the website 
**Web App can be viewed by this domain name**
**[Link to the online version](http://ec2-54-237-211-129.compute-1.amazonaws.com/restaurants)**
### Verify the domain name for Google OAuth
* Go to the google developer console
* Go to the Item Catalog app
* Copy-Paste the domain name of the app and click save.

**Go to grader terminal and run command : `sudo services apache2 restart`**
## Debugging
    If some error occurs while visiting the website online, the record of the error or output of your "print" functions will be in the `error.log` file located at `/var/log/apache2/`
 - - -
    


