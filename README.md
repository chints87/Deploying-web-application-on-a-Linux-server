# Linux server configuration to serve a Sports catalog web application
A web server and database server setup for a *Sports catalog* web application deployed on a AWS (Amazon Web Service) ubuntu system
***
### Get your server 
1) Set up a ubuntu system using **Amazon Lightsail** from Amazon Web Services 

2) Once the ubuntu instance has been setup, a IP address is provided. For this setup, **The Pulbic IP address here is 13.127.75.75 The hostname for this IP 
   address that serves this application is http://ec2-13-127-7-75.ap-south-1.compute.amazonaws.com**

 **LOGIN DETAILS FOR GRADER** ssh -i ~/.ssh/linuxProject.pub -p 2200 grader@13.127.7.75. There is no passphrase and password is 'grader'

3) By default, you will be logged into the ubuntu system with 'ubuntu'

4) Download the ssh key from the account ``` LightsailDefaultKey-ap-south-1.pem```and place it in your local system path

5) Change rights for the RSA key in your local system by typing ```chmod 600 ~/.ssh/ LightsailDefaultKey-ap-south-1.pem```

6) Using your Public IP address 52.66.208.68, log into the terminal ```ssh -i ~/.ssh/ LightsailDefaultKey-ap-south-1.pem ubuntu@13.127.75.75```

### Configuring and securing your server 

References [Udacity - Student hub](https://mena.udacity.com/)

By default, in order to ensure that your server is secure, follow the steps shown below

1) The commands belows show which packages have updates and then followed by the command when the upgrade of the installed packages takes place.

```
   sudo apt-get update
   sudo apt- get upgrade
```

  - Edit ssh configuration file ```sudo nano /etc/ssh/sshd_config``` AND NOT ```sudo nano /etc/ssh/ssh_config``` and change Port from 22 to 2200 and then ```sudo service ssh restart```

  - Go to Amazon Lightsail and enable port 22 and 2200

  - Go back to the terminal ```ssh -i ~/.ssh/LightsailDefaultKey-ap-south-1.pem -p 2200 ubuntu@13.127.7.75``` and connect using the -p 2200

  - Check intial status before any setting is done for the firewall
    ```
    sudo ufw status
    ```
    The result should show **Status: inactive**    

  - Initially set all incoming requests to be blocked   
    ```
    sudo ufw default deny incoming
    ```

  - Set all outgoing requests to be allowed 
    ```
    sudo ufw default allow outgoing
    ```

  - Set the following configurations to setup firewall
    ```
    sudo ufw allow 2200/tcp
    sudo ufw allow www
    sudo ufw allow 123/udp
    
    ```

  - Enable firwall ```sudo ufw enable```

  - Type ```sudo ufw status``` and you should see this 

  ```
  To                         Action      From
--                         ------      ----
2200/tcp                   ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
123/udp                    ALLOW       Anywhere
2200/tcp (v6)              ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
123/udp (v6)               ALLOW       Anywhere (v6)

```
 - Go back to the terminal ```ssh -i ~/.ssh/LightsailDefaultKey-ap-south-1.pem -p 2200 ubuntu@13.127.7.75``` and connect using the -p 2200


### Creating a user 

1) ```sudo adduser grader```

2) The password for grader is set to grader

3) To check if the user has been added install ```sudo apt install finger``` and then type ```finger grader``` and you will see details of the grader user that was created

```
Login: grader                           Name: Udacity Grader
Directory: /home/grader                 Shell: /bin/bash
Never logged in.
No mail.
No Plan.
```

4) To provide sudo rights to the grader type ```sudo visudo``` , locate ```root ALL=(ALL:ALL) ALL``` and below that enter ```grader ALL=(ALL:ALL) ALL```. Save the file and exit.

5) Open ```sudo cat /etc/sudoers``` and find the grader rights in it

6) You can check that you can open ```sudo cat /etc/passwd``` now. 

7) Now go to your local machine to generate a ssh key pair ```ssh-keygen``` and save it in your local .ssh directory. **important to generate ssh-keypair in the local machine**

```
The key fingerprint is:
SHA256:+C6aUhjXucuUUPLucNEJul7rHDJXYZ5EYk8s5LM1eNE Chintan_g@Chintan
The key's randomart image is:
+---[RSA 2048]----+
|     .+.+.       |
|    .o+*..E      |
|     *+=O.       |
|  . + =Xo+       |
|   + +o+S        |
|  . + Bo         |
|   ooBoo.        |
|  . .**o         |
|   .ooo..        |
+----[SHA256]-----+
```

8) Go to your host terminal and log in as grader ```sudo -i -u grader```

9) Make a SSH directory ```mkder .ssh``` and a file in that directory to store the public keys associated with the grader account ```touch .ssh/authorized_keys```

10) From your local machine open ```Chintan_g ~ $ cat .ssh/linuxProject.pub``` and copy the contents of the public key and go to the host machine ```nano .ssh/authorized_keys``` paste the contents

11) Save the file and change file permission to allow only grader to access the .ssh ```chmod 700 .ssh``` and to prevent anyone touching the authorized key```chmod 644 .ssh/authorized_keys```

12) Go to ```sudo nano /etc/ssh/sshd_confif``` and change PasswordAuthentication to yes. 

13) Exit out of the host system and login as follows, the passphrase is blank and password is *grader* to access as a grader into the system
``` 
 ssh -i ~/.ssh/linuxProject.pub -p 2200 grader@13.127.7.75
Enter passphrase for key '/c/Users/Chintan_g/.ssh/linuxProject.pub':
grader@13.127.7.75's password:
```

###Change timezone to UTC

1) Type this ```sudo timedatectl set-timezone UTC``` 

2) To check if timezone changed ```timedatactl status```
```
Local time: Sat 2019-01-26 12:02:20 UTC
  Universal time: Sat 2019-01-26 12:02:20 UTC
        RTC time: Sat 2019-01-26 12:02:20
       Time zone: UTC (UTC, +0000)
 Network time on: yes
NTP synchronized: yes
 RTC in local TZ: no
```


##Install and Configure Apache2 to serve a Python mod_wsgi application. The resources used to set up [apache2](https://www.digitalocean.com/community/tutorials/how-to-configure-the-apache-web-server-on-an-ubuntu-or-debian-vps#looking-at-the-apache2conf-file) and the [flask application](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps) 

1) Set up Apache2
```
sudo apt-get install apache2
``` 
2) Depending on which python version the catalog application has been setup, one will set up mod_wsgi that will connect the Apache2 webserver to the Flask Application
```
sudo apt-get install libapache2-mod-wsgi 
```
Then, enable wsgi ```sudo a2enmod wsgi```

3) Start the apache2 service ```sudo service apache2 start``` and go to the IP address 13.127.7.75 and you should see a **Apache default webpage that mentions "It works"**

4) Follow the steps to create a directory and a flask app
```
------www
-----------catalog
----------------catalog
-------------------static
-------------------templates
-------------------venv
-------------------__init__.py
---------------catalog.wsgi    
```
/var/www
```
cd /var/www
sudo mkdir catalog
```
/var/www/catalog
```
cd catalog
sudo mkdir catalog
```

5) Setting up a virtual enviornment to keep application and dependencies seperated from the main system

Install pip 
```
sudo apt-get install python-pip
```
Install virtualenv
```
sudo pip install virtualenv
```

6) Go to the directory ```cd /var/www/catalog/catalog/``` and create virtual enviornment by typing ```sudo virtual venv```

7) Activate the *venv* environment ```source venv/bin/activate```  and install *Flask* with ```pip install Flask``` and **NOT** ```sudo pip install Flask``` as this would install in the main system and not the virtual enviornment and then ```deactivate``` to get out of the virtual enviornment.


8) Configuring apache2 ```sudo nano /etc/apache2/sites-avialable/catalog.conf```

9) Copy and the paste in the file and save
```
<VirtualHost *:80>
                ServerName 13.127.7.75
                ServerAlias ec2-13-127-7-75.ap-south-1.compute.amazonaws.com
                ServerAdmin admin@13.127.7.75
                WSGIDaemonProcess catalog python-path=/var/www/catalog/ python-home=/var/www/catalog/catalog/venv/
                WSGIProcessGroup catalog
                WSGIApplicationGroup %{GLOBAL}
                WSGIScriptAlias / /var/www/catalog/catalog.wsgi
                <Directory /var/www/catalog/catalog/>                        
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/catalog/catalog/static
                <Directory /var/www/catalog/catalog/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
10) Enable the virtual host ```sudo a2ensite catalog```


11) Go to ```cd /var/www/catalog``` and make a wsgi file ```sudo nano catalog.wsgi``` and copy the following code 
```
activate_this = '/var/www/catalog/catalog/venv/bin/activate_this.py'
execfile(activate_this, dict(__file__=activate_this))

#!/usr/bin/python
import sys
import logging
import site
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/catalog/")

# Calculate path to site-packages directory.

python_version = '.'.join(map(str, sys.version_info[:2]))
site_packages = '/var/www/catalog/catalog/venv/lib/python%s/site-packages' % python_version

# Add the site-packages directory.

site.addsitedir(site_packages)

from catalog import app as application
application.run()
```

12) Then type ```sudo service apache2 restart``` for new configuration to take place

### Install and configure PostgreSQL

The references used here are for [installing PostgreSQL](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps) and [securing PostgreSQL in remote connection](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)

1) Install PostgreSQL ```sudo apt-get install postgresql postgresql-contrib```

2) Check the following settings to ensure there are no remote connections allowed to the database ```sudo nano /etc/postgresql/9.5/main/pg_hba.conf```

```
# Database administrative login by Unix domain socket
local   all             postgres                                peer

# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
```
8) Log in at posgres as postgres which is automatically created ```sudo -su postgres``` and enter ```psql``` 

In psql then enter the following commands to create a user, database and configure the database

9) ```CREATE ROLE catalog WITH PASSWORD 'catalog';``` You can check if a user is created by then typing ```\du```

10) ```ALTER ROLE catalog CREATEDB;``` Giving an attribute to the catalog user as to what it can do. You can check changes by typing ```\du```

11) ```CREATE DATABASE catalog WITH OWNER catalog;``` creates a database catalog with the user catalog. You can check by typing ```\l``` to see if the datbase was created. 

12) Now we will connect to the database ```\c catalog``` and ensure only catalog is allowed to create tables 
```
REVOKE ALL ON SCHEMA public FROM public;
GRANT ALL ON SCHEMA public TO catalog;
```
13) Close connection ```\q``` and ```exit``` from postgreSQL user.

###Install Git

1) ```sudo apt-get install git``` to install git

2) Make *grader* owner of the catalog directory by going first to ```cd /var/www/``` and then ```sudo chmod -R grader:grader catalog```

3) You need to ```sudo mv /var/www/catalog/catalog/venv /var/www/catalog/```

4) You need to go to ```cd \var\www\catalog\ ```  and remove catalog ```sudo rm -rf catalog```

5) Go to ```cd /var/www/catalog``` and ```git clone https://github.com/chints87/catalog.git catalog```

6) You need to ```sudo mv /var/www/catalog/venv /var/www/catalog/catalog```

7) Got to the root of the webserver ```\var\www\``` and ```sudo nano .htacces``` and in the file add and save ```RedirectMatch 404 /\.git```. This is to ensure no one can hack .git directory [Ubuntu](https://stackoverflow.com/questions/6142437/make-git-directory-web-inaccessible)

### Getting ready to deploy the web application 

1) Go to ```cd /var/www/catalog/catalog``` and change name of file ```sudo mv application.py to __init__.py```

2) In __init__.py change engine from sqlite to postgresql ```engine = create_engine('postgresql://catalog:catalog@localhost/catalog')```

3) Make the following changes to app.run by entering the IP address and the port number as shown
```
if __name__ == '__main__':
    app.secret_key = 'super_secret_key'
    app.debug = True
    app.run(host='13.127.7.75', port=80)
``` 

4) In the catalog_database_setup_users.py file change engine from sqlite to postgresql ```engine = create_engine('postgresql://catalog:catalog@localhost/catalog')```

5) Now go to ``` cd \var\www\catalog\catalog\``` and ```source venv/bin/activate``` to install packages in the virtual enviornment. Add httplib2,
oauth2cd ..client, sqlalchemy, psycopg2, sqlalchemy_utils, requests

6) Also in the __init__.py file change ```open('client_secrets.json', 'r').read())['web']['client_id']``` to ```open('/var/www/catalog/catalog/client_secrets.json', 'r').read())['web']['client_id']```

7) Then ```sudo service apache2 restart``` for changes to take effect


### Oauth Login

1) You will need to go to [Google API console](https://console.developers.google.com) and add the IP address and the
host name in the javascript origins and in the authorized URI you will need to add the hostname/oauth2callback

2) Download the new client_secrets.json file and update in the directory. 

3) Facbook issue with https requirement 

### Acknowledgements

I am very grateful to the following people for being able to reference their work that guided me well in this project.Some code here has been directly used, referred to or modified from the following:

1) [iliketomatoes](https://github.com/iliketomatoes/linux_server_configuration)
2) [chuanqin3](https://github.com/chuanqin3/udacity-linux-configuration)
3) [boisalai](https://github.com/boisalai/udacity-linux-server-configuration)
4) [thuynh8](https://github.com/thuynh8/linux-server-project)
5) [aaayumi](https://github.com/aaayumi/linux-server-configuration-udacity)
6) [Udacity](https://mena.udacity.com/)
7) [github](https://github.com/)
8) [Stackoverflow](https://stackoverflow.com/)
9) [askubuntu](https://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt)
10)[askubuntu](https://askubuntu.com/questions/767045/how-to-check-computers-timezone-through-terminal)
11)[Digitalocean](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04)
12)[Digitalocean-Setting up of mod_wsgi and flask](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
13)[Digitalocean](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)

