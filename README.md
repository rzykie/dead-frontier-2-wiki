# Dead Frontier 2 Wiki

This repository is used for the Dead Frontier 2 Wiki App developed using Wagtail.

## Local Environment Setup

### Required Installations

1. [Python 3.11](https://www.python.org/downloads/)  
    On macOS (with Homebrew): `brew install python3`
2. [Poetry 1.1.11](https://python-poetry.org/docs/#installation)  
    `curl -sSL https://install.python-poetry.org | python3 -`
3. [PostgreSQL 14.0](https://www.postgresql.org/download/)  
    On macOS (with Homebrew): `brew install postgres`

### Install Requirements

1. `poetry install`  
    This installs the libraries required for this project
2. `pre-commit install` 
    This installs pre-commit hooks to enforce code style.

### Setup PostgreSQL Database

```bash
sudo -u postgres psql

CREATE USER wiki WITH PASSWORD 'wiki';
ALTER USER wiki CREATEDB;

CREATE DATABASE wiki owner wiki;
```

### Configure .env File

1. Copy `.env.example` to `.env` and customize its values.
2. `SECRET_KEY` should be a random string, you can generate a new one using the following command:  
    `python -c 'from secrets import token_urlsafe; print("SECRET_KEY=" + token_urlsafe(50))'`
3. Set `DATABASE_URL` to `postgres://wiki:wiki@localhost/wiki`.

### Setup DB Schema

1. `./manage.py migrate`  
    This applies the migrations to your database
2. `./manage.py createsuperuser`  
    This creates your superuser account. Just follow the prompts.

## Running the App

1. `poetry shell`  
    If it's activated you'll see the virtual environment name at the beginning of your prompt, something like `("df2-wiki"-2wVcCnjv-py3.11)`.
2. `./manage.py runserver`

## Running the Celery Tasks
1. Make sure you have a Redis server running on localhost with the default port (6379). More information on how to setup [here](https://redis.io/docs/getting-started/installation/install-redis-on-mac-os/)
2. Set the `CELERY_BROKER_URL` env var to redis://localhost:6379/0
3. `celery -A agfirst worker -l info`
    This starts a Celery worker that will process the tasks defined in the agfirst application.
4. `celery -A agfirst beat -l info`
    This starts the Celery beat scheduler that will schedule and trigger periodic tasks defined in the agfirst application.
## Running the Tests

1. `poetry shell`
2. `pytest`  
    Run `pytest --cov=. --cov-report term-missing` to also show coverage report.

# FAQs

1. Changing the `.env` file variables has no effect.  
    Run `export $(grep -v '^#' .env | xargs -0)` to source the file  
    or  
    Exit shell and run `poetry shell` again to reload the env file
