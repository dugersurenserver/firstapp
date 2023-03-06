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




