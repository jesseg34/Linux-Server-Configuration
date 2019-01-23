# Linux Server Configuration
-------------
* Udacity Linux Server Configuration Project
* The server is hosted via Amazon's Lightsail base Ubuntu solution

Server Information
-------------
* IP address: `34.215.200.153`
* Web app url: `http://34.215.200.153/`

Configuration Summary / Software Used
-------------
1. Created new lightsail installation
2. Updating Available Packages
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
2. Added new grader user
    * `sudo adduser grader`
3. Gave grader sudo access
    * Create new sudoers file for the new user with `grader ALL=(ALL) NOPASSWD:ALL`
4. Generated SSH Key Pairs locally using 'ssh-keygen'
    * Installed the public key on the server at `~.ssh/authorized_key`
    * Set `.ssh` to permission 700 and `authorized_key` to 644
5. Disable Password Based Login
    * Edit /etc/ssh/sshd_conifg
    * Set `PasswordAuthenication` to `No`
6. Change SSH Port
    * Edit /etc/ssh/sshd_config
    * Uncomment and change `Port` from 22 to 2200
7. Install and configure Firewall
    * Install `UFW`
    * Open up the needed ports
        * `sudo ufw allow 2222/tcp`
        * `sudo ufw allow 80/tcp`
        * `sudo ufw allow 123/udf`
    * Enable Firewall `sudo ufw enable`
    * Configure Lightsail Firewall to match ufw rules
8. Database Setup
    * Install postgresql
    * Login as postgres `sudo su - postgres`
    * Enter shell `psql`
    * Create new user `CREATE USER catalog WITH PASSWORD 'supersecret';`
    * Create database: `CREATE DATABASE catalog WITH OWNER catalog;`
9. Install mod-wsgi
    * `sudo apt-get install libapache2-mod-wsgi` (for pyton 2)
    * Enable mod-wsgi with `sudo a2enmod wsgi
10. Install git and clone repo
    * Install git `sudo apt-get install git`
    * Clone catalog repo to `/var/www/`
    * Rename catalog base folder to `catalog` (remove the dash for compatibility)
    * Create wsgi file
    ```
    import sys

    sys.path.insert(0, '/var/www/catalog/')
    from app import app as application

    application.secret_key='super_secret_key'

    ```
11. Configure VirtualHost
    * Create file `/etc/apache2/sites-available/Catalog.conf`
    ```
    <VirtualHost *:80>
                WSGIScriptAlias / /var/www/catalog/catalog.wsgi
                <Directory /var/www/catalog/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
    ```
12. Install PIP and Depencies
    * `sudo apt-get install pip`
    * Install Depencies with `pip install [dep]`
        * Flask
        * Sqlalchemy
        * oauth2client
        * httplib2
        * requests
13. Start App
    * `sudo a2ensite Catalog`
14. Restart apache to complete
    * `service apache2 restart`

Credit / Resources Used During Build
------
* http://leonwang.me/post/deploy-flask
* http://flask.pocoo.org/docs/1.0/deploying/mod_wsgi/
