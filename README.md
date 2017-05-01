Linux-Server-Configuration

- In this project, a linux server has been configured with apache to serve the Catalog project site.

- To visit the server go to - IP Address

Reference  https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

Deploying the App 
1. Installed apache 
    - run command sudo apt-get update
    - run command sudo apt-get install apache2
2. Installed and enabled mod_wsgi 
    - cmd - sudo apt-get install libapache2-mod-wsgi python-dev
    - cmd - enable mod_wsgi sudo a2enmod wsgi

3. Installed Flask 
    - cmd - sudo apt-get install python-pip
    - cmd - sudo pip install Flask

4. A virtual host has been configured and enabled for the catalog app
    - cmd - sudo nano /etc/apache2/sites-available/catalog-app.conf
        - <VirtualHost *:80>
        - The ServerName directive sets the request scheme, hostname and port that
        - the server uses to identify itself. This is used when creating
        - redirection URLs. In the context of virtual hosts, the ServerName
        - specifies what hostname must appear in the request's Host: header to
        - match this virtual host. For the default virtual host (this file) this
        - value is not decisive as it is used as a last resort host regardless.
        - However, you must set it for any further virtual host explicitly.
        -ServerName www.example.com

        ServerAdmin webmaster@localhost

        - Define WSGI parameters. The daemon process runs as the www-data user.
        WSGIDaemonProcess catalog user=www-data group=www-data threads=5
        WSGIProcessGroup catalog
        WSGIApplicationGroup %{GLOBAL}

        - Define the location of the app's WSGI file
        WSGIScriptAlias / /srv/fullstack-nanodegree-vm/catalog/catalog/catalog.wsgi

        - Allow Apache to serve the WSGI app from the catalog app directory
        <Directory /srv/fullstack-nanodegree-vm/catalog/catalog/>
                Require all granted
        </Directory>

        - Setup the static directory (contains CSS, Javascript, etc.)
        Alias /static /srv/fullstack-nanodegree-vm/catalog/catalog/static

        - Allow Apache to serve the files from the static directory
        <Directory  /srv/fullstack-nanodegree-vm/catalog/catalog/static/>
                Require all granted
        </Directory>

        - Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        - error, crit, alert, emerg.
        - It is also possible to configure the loglevel for particular
        - modules, e.g.
        -LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost> 

5. Enabled the virtual host that was created 
    - cmd - sudo a2ensite catalog-app

6. Created a wsgi file to serve the flask app. 
    - the folder structure for my application is as below 
        | /src/fullstack-nanodegree-vm/
        | - catalog 
        | - catalog 
        |   ----template
        |   ----static
        |   ----catalogitems.py 
        |   ----catalog.wsgi
        |   ----client_secrets.json
        |   ----__init__.py  

    - the catalog wsgi file contains the system path to the catalog project 
    ` -!/usr/bin/python
        import sys
        import logging
        logging.basicConfig(stream=sys.stderr)
        sys.path.insert(0,"/srv/fullstack-nanodegree-vm/catalog")
        sys.path.append('/usr/bin/python')

        from catalog import app as application
        application.secret_key = 'Secret' `
7. The project.py file has been renamed to __init__.py in order for project to run the Flask app 
    
8. Restart the apache server to make sure that the app is working 
    - cmd - sudo service apache2 restart 

9. Created grader user, provided sudo access and created an ssh key 
    - cmd to create user
        - sudo addusr grader 
    - sudo access granted by creating file named grader in the /etc/sudoers.d directory 
        - cmd - sudo nano /etc/sudoers.d/grader 
            - added line grader ALL=(ALL:ALL) ALL 
    - created ssh key by running the ssh-keygen command on a local machine 
        - added public key on the virtual server 
            - created directory .ssh 
                - created authorized_keys file with public key information that was generated 
        - granted permission on my local machine for the key that was generated 
            -cmd chmod 700 .ssh and chmod 644 .ssh/authorized_keys 
10. SSH can be run to login as the grader user 
    - cmd - ssh -i <key location> grader@ipaddress -p 22000

11. Check for updates and install and packages
    - cmd sudo apt-get update 
    - cmd sudo apt-get upgrade 

12. Configured UFW firewall and changed ssh port from 22 to 2200
    - UFW only allows incoming connection for SSH on port 2200, HTTP on port 80 and NTP on port 123
    - sudo ufw allow 2200/tcp
    - sudo ufw allow 80/tcp
    - sudo ufw allow 123/udp 
    - UFW enabled using cmd- sudo ufw enable 

13. Configred the local timezone to use UTC
    -cmd sudo dpkg-reconfigure tzdata
        - choose none of the above then UTC 

14. Installed postgresql to create the catalog database 
    - reference: https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps 
    - sudo apt-get install postgresql 
    - installed psycopg2 with command 
        - cmd - sudo apt-get install postgresql python-psycopg2 
    * note - logged into superuser postgres using 
        - cmd sudo su - postgres
            - logged into psql with cmd psql 
        
    - created a database named catalog and user named catalog 
        - cmd - create database catalog;
        - cmd - create user catalog; 
        - created catalog user password 
            - cmd alter role catalog with password 'password';
        - granted catalog permission to the catalog database 
            - cmd grant all privileges on database catalog to catalog; 
    - create the database schema 
        - cmd - sudo python database_setup.py
