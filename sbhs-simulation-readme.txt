Procedure to host the simulator
-------------------------------
The folder src/lab/sbhs-simulation contains the simulator

Copy the folder sbhs-simulation in home folder

Add sudo in the begining of commands which give you a Permission Denied error

apt-get install apache2

apt-get install mysql

apt-get install python-pip python-virtualenv

apt-get install libapache2-mod-wsgi

apt-get install libmysqlclient-dev python-dev

apt-get install python-MySQLdb

apt-get install python-dev

sudo apt-get install mysql-client

apt-get install libmysqlclient-dev

cd sbhs-simulation
../env/bin/pip install -r requirements.txt

source ../env/bin/activate
pip install mysql-connector-python
python manage.py syncdb
python manage.py migrate
python manage.py runsyncdb


Add these lines to /etc/apache2/apache2.conf

<Directory ~/sbhs-simulation/source/sbhs_server>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
</Directory>

Add these lines to /etc/apache2/sites-enabled/000-default.conf

WSGIDaemonProcess sbhs-simulation python-path=~/sbhs-simulation/source:~/sbhs-simulation/env/lib/python2.7/site-packages
WSGIProcessGroup sbhs-simulation
Alias /static ~/sbhs-simulation/source/static/
        WSGIScriptAlias /sbhs-simulation/ ~/sbhs-simulation/source/sbhs_server/wsgi.py/
         <Directory "~/sbhs-simulation/source">
           Require all granted
         </Directory>


Do these edits in ~/sbhs-simulation/source/sbhs_server/settings.py :-

if not DEBUG:
    ALLOWED_HOSTS = [
        "localhost",
        "127.0.0.1",
        "vlabs.iitb.ac.in",
        "vlabs.iitb.ac.in.",
        "10.102.152.15",
        "10.102.6.217",
	"add_your_ip_or_domain",
    ]

is_production = hostname == "vlabsserver"
"vlabsserver" should be changed to your computer hostname

if is_production:
    DATABASES = {
       'default': {
            'ENGINE': 'django.db.backends.mysql', 
            'NAME': 'db_name',
            'USER': 'db_username',
            'PASSWORD': 'db_password',
            'HOST': 'localhost',
            'PORT': '3306',
        }
    }

EMAIL_HOST = 'your_smtp_address' #for example: smtp-auth.iitb.ac.in
EMAIL_PORT = 'your_smtp_port' # for example: 25
EMAIL_HOST_USER = "smtp_username"
EMAIL_HOST_PASSWORD = "smtp_password"

if is_production:
    BASE_URL = "http://<your_ip_address_or_domain>/sbhs-simulation"
    FORCE_SCRIPT_NAME = "/sbhs-simulation"
    USE_X_FORWARDED_HOST = True
else:
    BASE_URL = "http://127.0.0.1:8000/"


Run these commands:-

source ../env/bin/activate
pip install mysql-connector-python
python manage.py syncdb
python manage.py migrate
python manage.py runsyncdb


YOU SHOULD BE ABLE TO ACCESS THE SIMULATOR AT localhost/sbhs-simulation/ or <ip_address>/sbhs-simulation/   on your web browser

Follow these steps to configure the client code
-------------------------------------------------- 
open the file scilab_codes_arm/common_files/sbhsclient.py

change the following code

if config_options["base_link"] != "None" and len(config_options["base_link"]) > 2:
                link = config_options["base_link"]
            else:
                link = "http://<your_ip_address_or_domain>/"

except Exception:
            logging.exception("Error while loading config. Switching to default configuration.")
            link = "http://<your_ip_address_or_domain>/"
            proxy_handler = urllib2.ProxyHandler({})
            logging.debug("Proxy: None")
            logging.debug("BASE_LINK: " + link)

You can now follow the procedure of any experiment to test
