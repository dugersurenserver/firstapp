# Системийн орчноо бэлдэх

<details open>
<summary> 1. Орчноо бэлдэх </summary>

Ubuntu үйлдлийн систем

Албан ёсны угсралтын өмнөх бэлтгэл ажил
Үндсэн программ хангамжийг суулгаж, орчинг бүрдүүлэх

DMOJ нь Django framework болон NodeJS дээр ажилладаг тул Python болон NodeJS-г урьдчилан суулгасан байх шаардлагатай. Цаашилбал, кодыг ажиллуулж буй ажлын хуваарьт илгээсэн даалгавруудыг буфер болгохын тулд мессежийн дараалал шаардлагатай тул Redis мэдээллийн санг урьдчилан суулгасан болно.

```
sudo apt update && sudo apt upgrade -y
sudo apt install git gcc g++ make python3-dev python3-pip libxml2-dev libxslt1-dev zlib1g-dev gettext curl redis-server -y
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
sudo apt install nodejs
sudo npm install -g sass postcss-cli autoprefixer
```

</details>

---

<details>
<summary>Nodejs -ийг суулгах nvm </summary>



Now, activate all settings using the following command:

```
source ~/.bashrc

nvm --version
nvm install node
node --version
nvm install node --lts
nvm install 14.19.1
node --version
nvm ls
nvm ls-remote
nvm use 14.19.1
node --version
nvm run default --version

```
</details>


---

<details>
<summary> Өгөгдлийн санг суулгах </summary>

 Энд Mariadb мэдээллийн санг ашигладаг бөгөөд Ubuntu дээр суулгах нь маш тохиромжтой. Одоогийн Mariadb мэдээллийн санд суулгасны дараа хэрэглэгчээс root нууц үг оруулах шаардлагагүй тул шууд дотоод нэвтрэх нууц үг анхдагчаар хоосон байна.

```
sudo apt update
sudo apt install mariadb-server libmysqlclient-dev -y

sudo mysql -u root -p
mariadb> CREATE DATABASE dmoj DEFAULT CHARACTER SET utf8mb4 DEFAULT COLLATE utf8mb4_general_ci;
mariadb> GRANT ALL PRIVILEGES ON dmoj.* to 'dmoj'@'localhost' IDENTIFIED BY 'db123';
mariadb> exit

```

DMOJ-г зохион бүтээхдээ мэдээллийн сан нь урт индексийг ашигладаг тул мэдээллийн сангийн нэмэлт тохиргоог энд хийх шаардлагатай (албан ёсны баримт бичигт дурдаагүй бөгөөд энэ нь өөр өөр үйлдлийн системтэй холбоотой байж болно). 

```
/etc/mysql/mariadb.conf.d/50-server.conf 

```

файлын innodb тайлбарын байрлалд дараах гурван мөрийг нэмнэ.Өөрчлөлт дууссаны дараа хүчин төгөлдөр болохын тулд Mysql мэдээллийн санг дахин эхлүүлнэ үү. Нэмж дурдахад, дараа нь татаж авсан DMOJ төслийн үндсэн кодын **site/manage.py**  файлд дараах хоёр мөрийг нэмэх хэрэгтэй.

```
[mysqld]
...

innodb_file_format = Barracuda
innodb_file_per_table = 1
innodb_large_prefix
```


### Mysql мэдээллийн санг дахин эхлүүлнэ үү

```
sudo service mysql restart
```

```
#!/usr/bin/env python
import os
import sys

if __name__ == "__main__":
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "sampleproj.settings")

    # Дараах хоёр мөрийг нэмнэ үү
    from django.db.backends.mysql.schema import DatabaseSchemaEditor
    DatabaseSchemaEditor.sql_create_table += " ROW_FORMAT=DYNAMIC"

```

</details>


---



<details>
<summary>venv -ийг суулгах</summary>

venv (virtual environment)-ийг суулгах

```
python3 -m venv venv
. venv/bin/activate
```

</details>

---

<details>
<summary>site -г суулгах</summary>

Кодоо татаж аваад хамаарлыг суулгана уу

Энэ алхам нь DMOJ-ийн үндсэн төслийн кодыг Github-аас локал руу татаж аваад дэд төслийн git tracking, update кодыг нэмэх явдал юм. Хэрэв та шийдвэрийн серверийг Docker-ийн аргаар тохируулахаар шийдсэн бол салбараа солих шаардлагагүй гэдгийг энд тэмдэглэх нь зүйтэй. Үгүй бол та branch солих хэрэгтэй болно.

```
git clone https://github.com/DMOJ/site.git
cd site
git checkout v2.1.0  # Энэ алхамыг дараа нь шүүлтийн серверийг тохируулахын тулд pypi аргыг ашиглах үед л гүйцэтгэх шаардлагатай.
git submodule init
git submodule update
pip3 install -r requirements.txt
pip3 install mysqlclient

```
</details>

---



<details>
<summary> Тохиргоо хийх local_settings.py</summary>


dmoj фолдер дотор дараах файлыг (dmoj/local_settings.py) үүсгэх доторн дараах кодыг оруулна. 
```
local_settings.py
``` 

```
#####################################
########## Django settings ##########
#####################################
# See <https://docs.djangoproject.com/en/3.2/ref/settings/>
# for more info and help. If you are stuck, you can try Googling about
# Django - many of these settings below have external documentation about them.
#
# The settings listed here are of special interest in configuring the site.

# SECURITY WARNING: keep the secret key used in production secret!
# You may use this command to generate a key:
# python3 -c 'from django.core.management.utils import get_random_secret_key;print(get_random_secret_key())'
SECRET_KEY = 'This key is not very secure and you should change it.'

# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = True  # Change to False once you are done with runserver testing.

# Uncomment and set to the domain names this site is intended to serve.
# You must do this once you set DEBUG to False.
ALLOWED_HOSTS = ['dmoj.mn']

# Optional apps that DMOJ can make use of.
INSTALLED_APPS += (
)

# Caching. You can use memcached or redis instead.
# Documentation: <https://docs.djangoproject.com/en/3.2/topics/cache/>
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
    },
}

# Your database credentials. Only MySQL is supported by DMOJ.
# Documentation: <https://docs.djangoproject.com/en/3.2/ref/databases/>
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'dmoj',
        'USER': 'dmoj',
        'PASSWORD': 'db123',
        'HOST': '127.0.0.1',
        'OPTIONS': {
            'charset': 'utf8mb4',
            'sql_mode': 'STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION',
        },
    },
}

# Sessions.
# Documentation: <https://docs.djangoproject.com/en/3.2/topics/http/sessions/>
#SESSION_ENGINE = 'django.contrib.sessions.backends.cached_db'

# Internationalization.
# Documentation: <https://docs.djangoproject.com/en/3.2/topics/i18n/>
LANGUAGE_CODE = 'en-ca'
DEFAULT_USER_TIME_ZONE = 'America/Toronto'
USE_I18N = True
USE_L10N = True
USE_TZ = True

## django-compressor settings, for speeding up page load times by minifying CSS and JavaScript files.
# Documentation: <https://django-compressor.readthedocs.io/en/latest/>
COMPRESS_OUTPUT_DIR = 'cache'
COMPRESS_CSS_FILTERS = [
    'compressor.filters.css_default.CssAbsoluteFilter',
    'compressor.filters.cssmin.CSSMinFilter',
]
COMPRESS_JS_FILTERS = ['compressor.filters.jsmin.JSMinFilter']
COMPRESS_STORAGE = 'compressor.storage.GzipCompressorFileStorage'
STATICFILES_FINDERS += ('compressor.finders.CompressorFinder',)


#########################################
########## Email configuration ##########
#########################################
# See <https://docs.djangoproject.com/en/3.2/topics/email/#email-backends>
# for more documentation. You should follow the information there to define
# your email settings.

# Use this if you are just testing.
#EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'

# The following block is included for your convenience, if you want
# to use Gmail.
#EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
#EMAIL_USE_TLS = True
#EMAIL_HOST = 'smtp.gmail.com'
#EMAIL_HOST_USER = '<your account>@gmail.com'
#EMAIL_HOST_PASSWORD = '<your password>'
#EMAIL_PORT = 587

# To use Mailgun, uncomment this block.
# You will need to run `pip install django-mailgun` to get `MailgunBackend`.
#EMAIL_BACKEND = 'django_mailgun.MailgunBackend'
#MAILGUN_ACCESS_KEY = '<your Mailgun access key>'
#MAILGUN_SERVER_NAME = '<your Mailgun domain>'

# You can also use SendGrid, with `pip install sendgrid-django`.
#EMAIL_BACKEND = 'sgbackend.SendGridBackend'
#SENDGRID_API_KEY = '<Your SendGrid API Key>'

# The DMOJ site is able to notify administrators of errors via email,
# if configured as shown below.

# A tuple of (name, email) pairs that specifies those who will be mailed
# when the server experiences an error when DEBUG = False.
ADMINS = (
    ('Your Name', 'your.email@example.com'),
)

# The sender for the aforementioned emails.
SERVER_EMAIL = 'DMOJ: Modern Online Judge <errors@dmoj.ca>'


################################################
########## Static files configuration ##########
################################################
# See <https://docs.djangoproject.com/en/3.2/howto/static-files/>.

# Change this to somewhere more permanent, especially if you are using a
# webserver to serve the static files. This is the directory where all the
# static files DMOJ uses will be collected to.
# You must configure your webserver to serve this directory as /static/ in production.
STATIC_ROOT = '/tmp/static'

# URL to access static files.
STATIC_URL = '/static/'

# Uncomment to use hashed filenames with the cache framework.
#STATICFILES_STORAGE = 'django.contrib.staticfiles.storage.ManifestStaticFilesStorage'


############################################
########## DMOJ-specific settings ##########
############################################

## DMOJ site display settings.
SITE_NAME = 'DMOJ'
SITE_LONG_NAME = 'DMOJ: Modern Online Judge'
SITE_ADMIN_EMAIL = 'admin@example.com'
TERMS_OF_SERVICE_URL = '//dmoj.ca/tos/'  # Use a flatpage.

## Bridge controls.
# The judge connection address and port; where the judges will connect to the site.
# You should change this to something your judges can actually connect to
# (e.g., a port that is unused and unblocked by a firewall).
BRIDGED_JUDGE_ADDRESS = [('localhost', 9999)]

# The bridged daemon bind address and port to communicate with the site.
#BRIDGED_DJANGO_ADDRESS = [('localhost', 9998)]

## DMOJ features.
# Set to True to enable full-text searching for problems.
ENABLE_FTS = True

# Set of email providers to ban when a user registers, e.g., {'throwawaymail.com'}.
BAD_MAIL_PROVIDERS = set()

# The number of submissions that a staff user can rejudge at once without
# requiring the permission 'Rejudge a lot of submissions'.
# Uncomment to change the submission limit.
#DMOJ_SUBMISSIONS_REJUDGE_LIMIT = 10

## Event server.
# Uncomment to enable live updating.
#EVENT_DAEMON_USE = True

# Uncomment this section to use websocket/daemon.js included in the site.
#EVENT_DAEMON_POST = '<ws:// URL to post to>'

# If you are using the defaults from the guide, it is this:
EVENT_DAEMON_POST = 'ws://127.0.0.1:15101/' # үйл явдал илгээх

# These are the publicly accessed interface configurations.
# They should match those used by the script.
#EVENT_DAEMON_GET = '<public ws:// URL for clients>'
#EVENT_DAEMON_GET_SSL = '<public wss:// URL for clients>'
#EVENT_DAEMON_POLL = '<public URL to access the HTTP long polling of event server>'
# i.e. the path to /channels/ exposed by the daemon, through whatever proxy setup you have.

# Using our standard nginx configuration, these should be:
EVENT_DAEMON_GET = 'ws://127.0.0.1:15100/event/' # Үйл явдал авах
#EVENT_DAEMON_GET_SSL = 'wss://<your domain>/event/'  # Optional
#EVENT_DAEMON_POLL = '/channels/'

# If you would like to use the AMQP-based event server from <https://github.com/DMOJ/event-server>,
# uncomment this section instead. This is more involved, and recommended to be done
# only after you have a working event server.
#EVENT_DAEMON_AMQP = '<amqp:// URL to connect to, including username and password>'
#EVENT_DAEMON_AMQP_EXCHANGE = '<AMQP exchange to use>'

## Celery
CELERY_BROKER_URL = 'redis://127.0.0.1:6379' 
CELERY_RESULT_BACKEND = 'redis://127.0.0.1:6379'

## CDN control.
# Base URL for a copy of Ace editor.
# Should contain ace.js, along with mode-*.js.
ACE_URL = '//cdnjs.cloudflare.com/ajax/libs/ace/1.2.3/'
JQUERY_JS = '//cdnjs.cloudflare.com/ajax/libs/jquery/2.2.4/jquery.min.js'
SELECT2_JS_URL = '//cdnjs.cloudflare.com/ajax/libs/select2/4.0.3/js/select2.min.js'
SELECT2_CSS_URL = '//cdnjs.cloudflare.com/ajax/libs/select2/4.0.3/css/select2.min.css'

# A map of Earth in equirectangular projection, for timezone selection.
# Please try not to hotlink this poor site.
TIMEZONE_MAP = 'https://upload.wikimedia.org/wikipedia/commons/thumb/2/23/Blue_Marble_2002.png/1024px-Blue_Marble_2002.png'

## Camo (https://github.com/atmos/camo) usage.
#DMOJ_CAMO_URL = '<URL to your camo install>'
#DMOJ_CAMO_KEY = '<The CAMO_KEY environmental variable you used>'

# Domains to exclude from being camo'd.
#DMOJ_CAMO_EXCLUDE = ('https://dmoj.ml', 'https://dmoj.ca')

# Set to True to use https when dealing with protocol-relative URLs.
# See <https://www.paulirish.com/2010/the-protocol-relative-url/> for what they are.
#DMOJ_CAMO_HTTPS = False

# HTTPS level. Affects <link rel='canonical'> elements generated.
# Set to 0 to make http URLs canonical.
# Set to 1 to make the currently used protocol canonical.
# Set to 2 to make https URLs canonical.
#DMOJ_HTTPS = 0

## PDF rendering settings.
# Directory to cache the PDF.
#DMOJ_PDF_PROBLEM_CACHE = '/home/dmoj-uwsgi/pdfcache'

# Path to use for nginx's X-Accel-Redirect feature.
# Should be an internal location mapped to the above directory.
#DMOJ_PDF_PROBLEM_INTERNAL = '/pdfcache'

# Enable Selenium PDF generation.
#USE_SELENIUM = True

## Data download settings.
# Uncomment to allow users to download their data.
#DMOJ_USER_DATA_DOWNLOAD = True

# Directory to cache user data downloads.
# It is the administrator's responsibility to clean up old files.
#DMOJ_USER_DATA_CACHE = '/home/dmoj-uwsgi/datacache'

# Path to use for nginx's X-Accel-Redirect feature.
# Should be an internal location mapped to the above directory.
#DMOJ_USER_DATA_INTERNAL = '/datacache'

# How often a user can download their data.
#DMOJ_USER_DATA_DOWNLOAD_RATELIMIT = datetime.timedelta(days=1)


## ======== Logging Settings ========
# Documentation: https://docs.djangoproject.com/en/3.2/ref/settings/#logging
#                https://docs.python.org/3/library/logging.config.html#configuration-dictionary-schema
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'file': {
            'format': '%(levelname)s %(asctime)s %(module)s %(message)s',
        },
        'simple': {
            'format': '%(levelname)s %(message)s',
        },
    },
    'handlers': {
        # You may use this handler as an example for logging to other files.
        'bridge': {
            'level': 'INFO',
            'class': 'logging.handlers.RotatingFileHandler',
            'filename': '<desired bridge log path>',
            'maxBytes': 10 * 1024 * 1024,
            'backupCount': 10,
            'formatter': 'file',
        },
        'mail_admins': {
            'level': 'ERROR',
            'class': 'dmoj.throttle_mail.ThrottledEmailHandler',
        },
        'console': {
            'level': 'DEBUG',
            'class': 'logging.StreamHandler',
            'formatter': 'file',
        },
    },
    'loggers': {
        # Site 500 error mails.
        'django.request': {
            'handlers': ['mail_admins'],
            'level': 'ERROR',
            'propagate': False,
        },
        # Judging logs as received by bridged.
        'judge.bridge': {
            'handlers': ['bridge', 'mail_admins'],
            'level': 'INFO',
            'propagate': True,
        },
        # Catch all logs to stderr.
        '': {
            'handlers': ['console'],
        },
        # Other loggers of interest. Configure at will.
        #  - judge.user: logs naughty user behaviours.
        #  - judge.problem.pdf: PDF generation log.
        #  - judge.html: HTML parsing errors when processing problem statements etc.
        #  - judge.mail.activate: logs for the reply to activate feature.
        #  - event_socket_server
    },
}

## ======== Integration Settings ========
## Python Social Auth
# Documentation: https://python-social-auth.readthedocs.io/en/latest/
# You can define these to enable authentication through the following services.
#SOCIAL_AUTH_GOOGLE_OAUTH2_KEY = ''
#SOCIAL_AUTH_GOOGLE_OAUTH2_SECRET = ''
#SOCIAL_AUTH_FACEBOOK_KEY = ''
#SOCIAL_AUTH_FACEBOOK_SECRET = ''
#SOCIAL_AUTH_GITHUB_SECURE_KEY = ''
#SOCIAL_AUTH_GITHUB_SECURE_SECRET = ''

## ======== Custom Configuration ========
# You may add whatever Django configuration you would like here.
# Do try to keep it separate so you can quickly patch in new settings.

```



</details>

---


<details>
<summary>Ажиллаж байгаа эсэхийг</summary>

**Ажиллаж байгаа эсэхийг**
local_settings.py -ийг нэмээд дараа нь шалгаж үзэх

```
python3 manage.py check
```
</details>

---


<details>
<summary>static файлуудыг бүрдүүлэх</summary>

**Ажиллаж байгаа эсэхийг**
local_settings.py -ийг нэмээд дараа нь шалгаж үзэх

```
./make_style.sh
python3 manage.py collectstatic
python3 manage.py compilemessages
python3 manage.py compilejsi18n
python3 manage.py migrate

python3 manage.py loaddata navbar
python3 manage.py loaddata language_small
python3 manage.py loaddata demo

python3 manage.py createsuperuser

```
</details>

---



<details>
<summary>Setting up Celery</summary>

The DMOJ uses Celery workers to perform most of its heavy lifting, such as batch rescoring submissions. We will use Redis as its broker, though note that other brokers that Celery supports will work as well.

Start up the Redis server, which is needed by the Celery workers.

```
service redis-server start
```


Configure local_settings.py by uncommenting CELERY_BROKER_URL and CELERY_RESULT_BACKEND. By default, Redis listens on localhost port 6379, which is reflected in local_settings.py. You will need to update the addresses if you changed Redis's settings.

We will test that Celery works soon.

</details>

---



<details>
<summary>Running the server</summary>

At this point, you should attempt to run the server, and see if it all works.

```
python3 manage.py runserver 0.0.0.0:8000
```


You should Ctrl-C to exit after verifying.

Do not use runserver in production!

We will set up a proper webserver using nginx and uWSGI soon.

You should also test to see if bridged runs.

```
python3 manage.py runbridged
```


If there are no errors after about 10 seconds, it probably works. You should Ctrl-C to exit.

Next, test that the Celery workers run.

```
celery -A dmoj_celery worker
```
You can Ctrl-C to exit.
</details>

---



<details>
<summary>Setting up uWSGI</summary>

runserver is insecure and not meant for production workloads, and should not be used beyond testing. In the rest of this guide, we will be installing uwsgi and nginx to serve the site, using supervisord to keep site and bridged running. It's likely other configurations may work, but they are unsupported.

First, copy our uwsgi.ini (link). You should change the paths to reflect your install.

You need to install uwsgi.


```
django-admin startproject main_project
```


uwsgi.ini - файлыг site үндсэн фолдер дотор үүсгэх
```
uwsgi.ini
```

Дараах тохиргооны хэсгийг хуулах

```
[uwsgi]
# Socket and pid file location/permission.
uwsgi-socket = /tmp/dmoj-site.sock
chmod-socket = 666
pidfile = /tmp/dmoj-site.pid

# You should create an account dedicated to running dmoj under uwsgi.
#uid = dmoj-uwsgi
#gid = dmoj-uwsgi

# Paths.
chdir = /home/bd/mysystem/site
pythonpath = /home/bd/mysystem/venv
virtualenv = /home/bd/mysystem/venv

# Details regarding DMOJ application.
protocol = uwsgi
master = true
env = DJANGO_SETTINGS_MODULE=dmoj.settings
module = dmoj.wsgi:application
optimize = 2

# Scaling settings. Tune as you like.
memory-report = true
cheaper-algo = backlog
cheaper = 3
cheaper-initial = 5
cheaper-step = 1
cheaper-rss-limit-soft = 201326592
cheaper-rss-limit-hard = 234881024
workers = 7
```



sudo service redis-server start

#将项目配置文件中Celery配置去除注释使其生效

# 测试运行主项目代码
python3 manage.py runserver 0.0.0.0:8000

# 运行上一步成功后，运行调度程序，十秒内无任何回显则ctrl+c中止
python3 manage.py runbridged

# 运行Celery任务队列，无错误回显即可
pip3 install redis
celery -A dmoj_celery worker

</details>

---




<details>
<summary>2. ?????????</summary>

???????

**Жишээ нь:**

```
django-admin startproject main_project
```
</details>

---




<details>
<summary>hosts  үүсгэх</summary>


**hosts  үүсгэх**

```
nano /etc/hosts
```

Үүгэх домэйн нэрээ зааж өгнө.


```
127.0.0.1 dmoj.mn

```
</details>

---




<details>
<summary>nginx-ийг суулгах тохируулах</summary>

nginx-ийг суулгах 

```
apt install nginx
```

proxy  - серверийн тохиргоо хийх

Үүний тулд root эрхээр орж тохиргоо хийх хэрэгтэй

```
sudo su

cd /etc/nginx/conf.d

sudo nano /etc/nginx/sites-available/dmoj.mn
```

Дотор нь дараах кодыг оруулж өгнө.


```
server {
    listen       80;
    listen       [::]:80;

    # Change port to 443 and do the nginx ssl stuff if you want it.

    # Change server name to the HTTP hostname you are using.
    # You may also make this the default server by listening with default_server,
    # if you disable the default nginx server declared.
    server_name dmoj.mn;

    add_header X-UA-Compatible "IE=Edge,chrome=1";
    add_header X-Content-Type-Options nosniff;
    add_header X-XSS-Protection "1; mode=block";

    charset utf-8;
    try_files $uri @icons;
    error_page 502 504 /502.html;

    location ~ ^/502\.html$|^/logo\.png$|^/robots\.txt$ {
        root /home/bd/mysystem/site;
    }

    location @icons {
        root /home/bd/mysystem/site/resources/icons;
        error_page 403 = @uwsgi;
        error_page 404 = @uwsgi;
    }

    location @uwsgi {
        uwsgi_read_timeout 600;
        # Change this path if you did so in uwsgi.ini
        uwsgi_pass unix:///tmp/dmoj-site.sock;
        include uwsgi_params;
        uwsgi_param SERVER_SOFTWARE nginx/$nginx_version;
    }

    location /static {
        gzip_static on;
        expires max;
        #root /tmp/static/;
        # Comment out root, and use the following if it doesn't end in /static.
        alias /tmp/static/; # 配置主项目的静态文件地址
    }

    # Uncomment if you are using PDFs and want to serve it faster.
    # This location name should be set to DMOJ_PDF_PROBLEM_INTERNAL.
    #location /pdfcache {
    #    internal;
    #    root <path to pdf cache diretory, without the final /pdfcache>;
    #    # Default from docs:
    #    #root /home/dmoj-uwsgi/;
    #}

    # Uncomment if you are allowing user data downloads and want to serve it faster.
    # This location name should be set to DMOJ_USER_DATA_INTERNAL.
    #location /datacache {
    #    internal;
    #    root <path to data cache diretory, without the final /datacache>;
    #
    #    # Default from docs:
    #    #root /home/dmoj-uwsgi/;
    #}

    # Uncomment these sections if you are using the event server.
    location /event/ {
        proxy_pass ;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_read_timeout 86400;
    }

    location /channels/ {
        proxy_read_timeout          120;
        proxy_pass ;
    }
}

```

Тухайн домэйн хаягийг бүртгэх

```
sudo ln -s /etc/nginx/sites-available/dmoj.mn /etc/nginx/sites-enabled/
```

Алдаа гарсан эсэхийг шалгах

```
sudo nginx -t
```

nginx системийг дахин эхлүүлэх

```
sudo systemctl restart nginx
```
</details>

---


<details>
<summary>websocket-client-ийг тохируулах</summary>


npm install qu ws simplesets
pip3 install websocket-client

# 重启程序使修改的配置生效
sudo supervisorctl update
sudo supervisorctl restart bridged
sudo supervisorctl restart site
sudo nginx -s reload

</details>



<details>
<summary>Тохиргоо хийх</summary>


Тохиргооны файлыг дараах байдлаар тохируулна

```
location /event/ {
        proxy_pass http://127.0.0.1:15100/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_read_timeout 86400;
    }

    location /channels/ {
        proxy_read_timeout          120;
        proxy_pass http://127.0.0.1:15102;
    }
}

```
</details>

---




<details>
<summary>site, celery, bridged config</summary>

root хэрэглэгчээр орж дараах фолдер дотор 3 файл үүсгэнэ
```
cd /etc/supervisor/conf.d

ls
```
Файлаа үүсгэх
```
nano site.conf
```
virtual орныг бүрдүүлсэн зам  
```
/home/bd/mysystem/venv

```
сайтыг суулгасан үндсэн зам 2ыг тохируулж өгнө.
```
/home/bd/mysystem/site
```


```
[program:site]
command=/home/bd/mysystem/venv/bin/uwsgi --ini uwsgi.ini
directory=/home/bd/mysystem/site
stopsignal=QUIT
stdout_logfile=/tmp/site.stdout.log
stderr_logfile=/tmp/site.stderr.log

```


bridged.conf  -ийг тохируулах

```
nano bridged.conf
```
Энд сайтын зам болон виртуал орчны замыг тохируулахаас гадна

идэвхитэй хэрэглэгчийн нэр root нэрийг зааж өгнө
```
[program:bridged]
command=/home/bd/mysystem/venv/bin/python manage.py runbridged
directory=/home/bd/mysystem/site
stopsignal=INT
# You should create a dedicated user for the bridged to run under.
user=bd
group=root
stdout_logfile=/tmp/bridge.stdout.log
stderr_logfile=/tmp/bridge.stderr.log
```

celery.conf -ийг тохируулж өгнө.
```
nano celery.conf
```

```
[program:celery]
command=/home/bd/mysystem/venv/bin/celery -A dmoj_celery worker
directory=/home/bd/mysystem/site
# You should create a dedicated user for celery to run under.
user=bd
group=root
stdout_logfile=/tmp/celery.stdout.log
stderr_logfile=/tmp/celery.stderr.log
```

Тохиргоо хийж дууссаны дараа дараах командуудыг ажиллуулж хэвийн эсэхийг шалгана.

```
supervisorctl update
supervisorctl status


nginx -t
service nginx reload


```
</details>

---



# Серверийг суулгах


<details>
<summary>2. ?????????</summary>

Сервер суулгах

```
sudo apt install python3-dev python3-pip build-essential libseccomp-dev -y
pip3 install dmoj
```


Дараах командыг өгснөөр тохиргоонуудыг автоматаар хийх ёстой
```
dmoj-autoconf
```
</details>

---




<details>
<summary>Docker-compose -ийг суулгах</summary>


```
sudo curl -sSL https://get.daocloud.io/docker | sh
sudo curl -sSL get.docker.com | sh
```
</details>

---




<details>
<summary>judge - ийг суулгах</summary>


```
git clone --recursive https://github.com/DMOJ/judge.git
cd judge/.docker
make judge-tier1
docker run \
    -v /mnt/problems:/problems \
    --cap-add=SYS_PTRACE \
    dmoj/judge-tier1:latest \
    cli -c /problems/judge.yml

```
</details>

---





id: firstJudge
key: k6szBqoq9+ETKfuYW5dIQOiP0mpQVEaXGPQfOWp2glE8V36P3luhQzaBzNpCIc/89Tcku0JEcUAMVniJB3XI9BXzL3SNUKkIS+Jy
problem_storage_root:
  - /home/ubunut/problem
runtime:





docker run \
    --name firstJudge \
    -p 127.0.0.1:9998:9998 \
    -v /mnt/problems:/problems \
    --cap-add=SYS_PTRACE \
    -d \
    --restart=always \
    dmoj/judge-tier1:latest \
    run 127.0.0.1 

    
    
    https://sibunglon.com/2020/07/26/database-returned-an-invalid-value-in-queryset.datetimes/
    
    ```
    mysql_tzinfo_to_sql /usr/share/zoneinfo | sed -e "s/Local time zone must be set--see zic manual page/local/" | mysql -u root mysql
    ```
    
    
    
    DMOJ_PROBLEM_DATA_ROOT = "/mnt/problems"
    
    
    
    
    netstat -tulnp | grep LISTEN
    
    
    
