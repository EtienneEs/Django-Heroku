# Django-Heroku
How to deploy a Django Project on Heroku

## Install Git Bash
- Install Git Bash  
  - git-scm.com
  - all defaults
  - Use git and optional linux tools to the Windows command prompt
  - all default settings from there on  

## create a SSH Key
- make a hidden ssh directory
- create an ssh-key in this directory

````
mkdir .ssh
cd .ssh
ssh-keygen.exe  
````
- press Enter and optionally set a password  

## Install Heroku CLI/toolbelt
[The Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli#download-and-install)
Use default settings ( set PATH to heroku )  

Check that heroku got installed
_Git bash terminal:_
````
heroku --version
````
## Virtualenv  

````
cd /c/
# create a new folder which holds our projects
mkdir projects
# go into folder
cd projects
# make sure you are in the folder
pwd
# check which modules are installed
pip freeze
# install virtualenvironment
pip install virtualenv
````

Now your virtualenvironment has been installed. In order to create a new environment:  

````
# use:
virtualenv venv
# if it does not work:
python -m virtualenv venv
````
In order to activate the virtual environment:
````
. venv/Scripts/activate
# or
source venv/scripts/activate
````

In order to deactivate the virtual environment:
````
deactivate
````

Install django in the environment:
````
pip install django
````

Create a new django project:
````
django-admin startproject yourprojectname

cd yourprojectname

# make sure it runs:
python manage.py runserver
````

## Creating a small application

````
python manage.py startapp address_book
````
- add app in settings.py to installed apps:
````python
mysite/settings.py
INSTALLED_APPS = [
  'django.contrib.admin',
  'django.contrib.auth',
  'django.contrib.contenttypes',
  'django.contrib.sessions',
  'django.contrib.messages',
  'django.contrib.staticfiles',
  'address_book',
]
````

- create address_book/urls.py

```` python
address_book/urls.py

from django.urls import path
from . import views

urlpatterns = [

]

````  

- include address_book/urls.py in mysite/urls.py:  


```` python
mysite/urls.py   

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('address_book.urls')),
]

````


- make migrations:  
````
python manage.py migrate
python manage.py createsuperuser
````

## Git -necessary for Heroku

Use git init to initialize your repository.
_if you know how to do that skip to [Heroku](#Heroku)_

- git config --global user.name "Your Name"
- git config --global user.email "your-email-address"
- git config --global alias.co checkout # allows you to write just git co instead of git checkout !!
- git init
- git add .
- git commit -m"Initial commit"

<a name="Heroku"></a>
## Heroku
- Create an account for Heroku (PAAS)
- log into heroku:
````
heroku login  
````
- if it stopped working (git bash problem) -> ctrl + c  
- connect your ssh key with heroku :
````
heroku keys:add
````
- now we can create an app:
````
heroku create
````


- Login into Heroku
- select your app
- Reveal Config Vars
- Copy your Secret Key from mysite.settings.py into the Value
- name the key: SECRET_KEY
- Add it

- install Gunicorn (webserver used by Heroku for deploying django)
- install django-heroku
- install python-decouple (to hide secret keys in your files)
````
pip install gunicorn
pip install django-heroku
pip install python-decouple
````

Now we need to tweak the settings file:

````
# mysite/mysite/settings.py:

import os
import django_heroku
from decouple import config
import dj_database_url # takes care of PW Username changes on heroku

............

# at the end of the settings file:
django_heroku.settings(locals())
````

### Hiding secret key in environment
Let's start to hide our secret key in the environment:
- We already installed and imported python-decouple
- now we need to create a .env file (touch .env)
- Cut & Paste the secret key into .env
- (add .env into your gitignore file so github does not import it)

### Whitenoise - Static files
(CSS, javascripts .. )

- Create a new folder - "static" in the "root" directory (mysite/static)
- create a new file: mysite/static/__ init__.py

adjust your settings file and include the static folder:
````
mysite/mysite/settings.py

# At the end of the settings file:

STATIC_URL = '/static/'
STATICFILES_DIRS = [
  os.path.join(BASE_DIR, 'static')
]

STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

````

Now we can add whitenoise into the middleware:

````
mysite/mysite/settings.py

MIDDLEWARE = [
  'django.middleware.security.SecurityMiddleware',
  'whitenoise.middleware.WhiteNoiseMiddleware',
  '........',

]
````

### Procfile & WSGI

- touch Procfile  

````
# mysite/Procfile
web: gunicorn mysite.wsgi

````

### Requirements file
Now we need to create a requirements file - so Heroku knows what to install:  

````  
pip freeze > requirements.txt
````

It will create a requirements file under mysite/requirements.txt containing all installed modules

### Push Code up to Heroku

update your git repository:
````
git add .

git commit -am "Push to Heroku"

git push heroku master
````
It should work - if something does not work checkout the error message or use:
````
heroku logs

heroku restart
````

- check if Heroku added a postgres database:
if not you can enter into the commandprompt:
````
heroku addons:add heroku-postgresql  
````
Congratulations - your app should be running in the cloud !!

### Postgres database on Heroku
checkot the Postgres database and checkout the settings :
Here you can see the credentials. As policy the database is redeployed every 24hours so every
24 hours the credentials are changing!!
The changing db credentials are handeled with / by dj_database_url

In order to login to the admin area of the app we need to create a superuser in the cloud:

In order to use the command line in heroku use heorku run:  

````
heroku run python manage.py makemigrations

heroku run python manage.py migrate

heroku run python manage.py createsuperuser

````

Now the database has been set up on heroku.

### How to include Postgres database in your local settings.py
_to be continued..._
