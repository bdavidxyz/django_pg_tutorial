# Libratop, demo of the simplest possible Django app with Linux and PostGre

In this tutorial, "libratop" is the commercial name of the product we want to build. It is an online library, so it will manage books.

## Prerequisites

- Tested on Linux Mint 21.3 Cinnamon
- Python & Pip

Ensure that "py" points to python 3

```shell
py --version
# Python 3.10.12
```

```shell
pip --version
# pip 22.0.2
```

- PostgreSQL

```shell
sudo -u postgres psql postgres -c 'SELECT version()' | grep PostgreSQL
# PostgreSQL 16.4
```

The following apt package may be required :

- python3-venv
- python3-dev
- libpq-dev
- postgresql
- postgresql-contrib

```shell
sudo apt install python3-venv python3-dev libpq-dev postgresql postgresql-contrib
```

## Create a new folder

```shell
mkdir libratop && cd libratop
```

## Add .gitignore and start git

Add the following .gitignore

```shell
.venv
*.sqlite3
__pycache__
```

And if `git status` returns an error, initialize a git repo like this

```shell
git init
```

## Prepare PostgreSQL

```shell
sudo -u postgres psql
```

```sql
CREATE DATABASE libratop;

CREATE USER libratopuser WITH PASSWORD 'password';

ALTER ROLE libratopuser SET client_encoding TO 'utf8';
ALTER ROLE libratopuser SET default_transaction_isolation TO 'read committed';
ALTER ROLE libratopuser SET timezone TO 'UTC';

GRANT ALL PRIVILEGES ON DATABASE libratop TO libratopuser;
\q

```

## Create virtual env and activate it

```shell
py -m venv .venv
source .venv/bin/activate
```

## Install django and pg driver

```shell
py -m pip install django psycopg2-binary
```

## Build django skeleton

Below, "core" means the core of our app, "." means build skeleton inside the current folder :

```shell
django-admin startproject core .
```

## Change settings.py

Inside settings.py, add localhost to the list of allowed hosts, like this :

```py
ALLOWED_HOSTS = ['localhost', '127.0.0.1']
```

And database settings like this :

```py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'libratop',
        'USER': 'libratopuser',
        'PASSWORD': 'password',
        'HOST': 'localhost',
        'PORT': '',
    }
}
```

## Initial migrations

Django comes with default tables (to manage users and authorizations notably), so let's inject them into our new db :

```shell
py manage.py makemigrations
py manage.py migrate
```

## Create a superadmin locally

Create a super user, just enter the following command and enter some realistic values when asked :

```shell
py manage.py createsuperuser
```

## Run local server

```shell
py manage.py runserver
```

Now open your browser at localhost:8000/admin and log in with the credentials of the super user you just created.

## Clean & commit

If everything is working well, you can commit changes

```shell
git add . && git commit -m "first commit"
```
