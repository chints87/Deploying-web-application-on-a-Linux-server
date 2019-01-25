# Linux-server-project

# Linux server configuration to serve a Sports catalog web application
A web server and database server setup for a *Sports catalog* web application deployed on a AWS (Amazon Web Service) ubuntu system
***
### Get your server

1) Set up a ubuntu system using **Amazon Lightsail** from Amazon Web Services 

2) Once the ubuntu instance has been setup, a IP address is provided. For this setup, The IP address here is 13.127.201.202. The hostname for this IP 
   address that serves this application is http://ec2-13-127-201-202.ap-south-1.compute.amazonaws.com

3) By default, you will be logged into the ubuntu system with 'ubuntu' user name The SSH required. 

### Secure your server 

By default, in order to ensure that your server is secure, follow the steps shown below

1) The commands belows show which packages have updates and then followed by the command when the upgrade of the installed packages takes place.

```
   sudo apt-get update
   sudo apt- get upgrade
```

2) Ubuntu comes with pre-installed firewall called UFW

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



1) Set up Apache2
```
sudo apt-get install apache2
``` 
2) Set up mod_wsgi that will connect the Apache2 webserver to the Flask Application
```
sudo apt-get install libapache2-mod-wsgi 
```
3) Follow the steps to create a directory and a flask app
```
cd /var/www
sudo mkdir catalog
cd catalog
sudo mkdir catalog
cd catalog
sudo mkdir static templates
sudo nano __init__.py (should I include this? )
sudo apt-get install python-pip
sudo pip install virtualenv
sudo virtual venv
source venv/bin/activate
sudo pip install Flask
sudo python __init__.py (should I include this?)
deactivate (should I include this?)
```
4) Configuring apache2 
```
sudo nano /etc/apache2/sites-available/catalog
sudo nano /etc/apache2/sites-avialable/catalog.conf
<VirtualHost *:80>
		ServerName mywebsite.com
		ServerAdmin admin@mywebsite.com
		WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
		<Directory /var/www/FlaskApp/FlaskApp/>
			Order allow,deny
			Allow from all
		</Directory>
		Alias /static /var/www/FlaskApp/FlaskApp/static
		<Directory /var/www/FlaskApp/FlaskApp/static/>
			Order allow,deny
			Allow from all
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
5)

```
sudo a2ensite catalog
cd /var/www/catalog
sudo nano catalog.wsgi
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalog import app as application
application.secret_key = 'Add your secret key'
```

6) 
```
sudo service apache2 restart
```

7) Check for remote connections not allowed 
```
sudo \etc\postgresql\9.5\main\pg_hba.conf
```

8) Log in at posgres and enter psql 
```
CREATE ROLE catalog WITH (PERMMISSIONS)
```
``` To view the different roles
\h CREATE ROLE
```

### Acknowledgment 

Some code here has been directly used, referred to or modified from the following:

1) [Udacity](https://mena.udacity.com/)
2) [github](https://github.com/)
3) [Stackoverflow](https://stackoverflow.com/)
4) [askubuntu](https://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt)
5) [Digitalocean](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04)
6) [Digitalocean](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps) - Setting up of mod_wsgi and flask
7) [Digitalocean](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)

