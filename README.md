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
````Python
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

- create address_book/urls.py
````python
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

## Git -necessary for Heroku

Use git init to initialize your repository.
_if you know how to do that skip to the next part_

- git config --global user.name "Your Name"
- git config --global user.email "your-email-address"
- git config --global alias.co checkout # allows you to write just git co instead of git checkout !!
- git init
- git add .
- git commit -m"Initial commit"

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
