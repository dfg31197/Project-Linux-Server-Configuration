# Project : Linux Server Configuration
- - -
## Credentials
**SSH Port:** 2200
**Public IP :** 13.59.179.98

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
#### Update and Upgrade Packages
* Update :  `sudo apt-get update`
* Upgrade : `sudo apt-get upgrade`
