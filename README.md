# Linux Server Configuration Project
IP address `3.121.58.54` port `2200`,
Complete URL for Web Application `3.121.58.54/catalog`

# Applications and Configuration
## Applications Installed
* `sudo apt-get install libapache2-mod-wsgi-py3`
* `sudo apt-get install apache2`
* `sudo apt-get install python-pip`
* `sudo apt-get install python-flask`
* `sudo pip install requests httplib2`
* `sudo apt-get install postgresql`
* `sudo apt-get install git`
* `pip install flask-httpauth`
## Configuration
* Configure `ufw` firewall to only allow incoming connections to ports `2200, 80, 123`
* I changed SSH port 22 to 2200 by editing the configuration file for sshd `etc/ssh/sshd_config`
after making sure the AWS instance firewall allows incoming connection to port 2200
* I created a `wsgi` file in the same directory as `ItemCatalogApp` to serve the flask app with the following content
and I named it myapp.wsgi.    

`import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,'/var/www/ItemCatalogApp')
from application import app as application`

* I created a Configuration file for apache in this directory /etc/apache2/sites-available
and I named it myapp.conf   
`<VirtualHost *:80>
     ServerName  3.121.58.54
     #ServerAdmin email address
     WSGIScriptAlias / /var/www/ItemCatalogApp/myapp.wsgi
     #Allow Apache to serve the WSGI app from ItemCatalogApp directory
     <Directory /var/www/ItemCatalogApp>
          Order allow,deny
          Allow from all
     </Directory>
     #Allow Apache to deploy static content
     <Directory /var/www/ItemCatalogApp/static>
        Order allow,deny
        Allow from all
     </Directory>
     ErrorLog ${APACHE_LOG_DIR}/error.log
     CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>`

* Disable the default apache site, and enable my flask app
 `sudo a2dissite 000-default.conf`
`sudo a2ensite myapp.conf`
then restart the service `sudo service apache2 reload`

* After Creating a new database user named `catalog` I made minor changes to `application.py` and `models.py`.

`engine = create_engine('postgresql://catalog:catalog@localhost/catalog')`

## Grader
* username `grader`
* SSH key Location
`/home/grader/.ssh`

## Reference
* https://ae.godaddy.com/help/changing-the-ssh-port-for-your-linux-server-7306
* https://serverfault.com/questions/309052/check-if-port-is-open-or-closed-on-a-linux-server
* https://www.cyberciti.biz/faq/unix-linux-check-if-port-is-in-use-command/
* https://docs.sqlalchemy.org/en/13/faq/sessions.html#this-session-s-transaction-has-been-rolled-back-due-to-a-previous-exception-during-flush-or-similar
* http://man7.org/linux/man-pages/man5/sshd_config.5.html
