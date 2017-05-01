# Linux-Server-Configuration

- In this project, a linux server has been configured with apache to serve the Catalog project site.

- To visit the server go to - IP Address

## Deploying the App 

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

```
<VirtualHost *:80>
           The ServerName directive sets the request scheme, hostname and port that
           the server uses to identify itself. This is used when creating
           redirection URLs. In the context of virtual hosts, the ServerName
           specifies what hostname must appear in the request's Host: header to
           match this virtual host. For the default virtual host (this file) this
           value is not decisive as it is used as a last resort host regardless.
           However, you must set it for any further virtual host explicitly.
          ServerName www.example.com

        ServerAdmin webmaster@localhost

           Define WSGI parameters. The daemon process runs as the www-data user.
        WSGIDaemonProcess catalog user=www-data group=www-data threads=5
        WSGIProcessGroup catalog
        WSGIApplicationGroup %{GLOBAL}

           Define the location of the app's WSGI file
        WSGIScriptAlias / /srv/fullstack-nanodegree-vm/catalog/catalog/catalog.wsgi

           Allow Apache to serve the WSGI app from the catalog app directory
        <Directory /srv/fullstack-nanodegree-vm/catalog/catalog/>
                Require all granted
        </Directory>

           Setup the static directory (contains CSS, Javascript, etc.)
        Alias /static /srv/fullstack-nanodegree-vm/catalog/catalog/static

           Allow Apache to serve the files from the static directory
        <Directory  /srv/fullstack-nanodegree-vm/catalog/catalog/static/>
                Require all granted
        </Directory>

           Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
           error, crit, alert, emerg.
           It is also possible to configure the loglevel for particular
           modules, e.g.
          LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

5. Enabled the virtual host that was created 
 - cmd - sudo a2ensite catalog-app

6. Created a wsgi file to serve the flask app. 
    - the folder structure for my application is as below 
      -  | /src/fullstack-nanodegree-vm/
      -  | - catalog 
      -  | - catalog 
      -  |   ----template
      -  |   ----static
      -  |   ----catalogitems.py 
      -  |   ----catalog.wsgi
      -  |   ----client_secrets.json
      -  |   ----__init__.py  

    - the catalog wsgi file contains the system path to the catalog project 
```
!/usr/bin/python
        import sys
        import logging
        logging.basicConfig(stream=sys.stderr)
        sys.path.insert(0,"/srv/fullstack-nanodegree-vm/catalog")
        sys.path.append('/usr/bin/python')

        from catalog import app as application
        application.secret_key = 'Secret'
```

