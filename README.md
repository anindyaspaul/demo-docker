# Demo Docker

As presented at [Digital Product School](https://digitalproductschool.io/).

_You might need to add sudo before the docker commands._

_Feel free to contact me for questions/help._

## Prerequisites

Install `docker` and `docker-compose` so that you can see the version names from the following commands.

```bash
docker -v
docker-compose -v
```

Now you can do either of the following.

1. Build and run the project from this repo
2. Create the django project from scratch

## 1. Build and run the project from the repo

### Clone and get into the project

```bash
git clone https://github.com/anindyaspaul/demo-docker.git
cd demo-docker
```

### Run the services

```bash
docker-compose up --build
```

Visit [localhost:8000](http://localhost:8000)

[Create the db tables (optional)](#user-content-run-django-migrations-to-create-db-tables)

[Create a superuser (optional)](#user-content-create-a-superuser-for-the-admin)

### Take down the services

```bash
docker-compose down
```

## 2. Create project from scratch

### Create project directory

```bash
mkdir demo-docker
cd demo-docker
```

### List django dependencies

Create `requirements.txt` with the following content.

```
Django
psycopg2
```

### Create `Dockerfile`

```Dockerfile
FROM python:3
ENV PYTHONUNBUFFERED 1
RUN mkdir /code
WORKDIR /code
COPY requirements.txt /code/
RUN pip install -r requirements.txt
COPY . /code/
```

### Build docker image

```bash
docker build -t demo .
```

### List docker images

```bash
docker images
```

### Create django project using django-admin

```bash
docker run -v ~/codes/dps/demo-docker:/code demo django-admin startproject demo .
```

### Run the project

```bash
docker run -v ~/codes/dps/demo-docker:/code -p 8000:8000 demo python manage.py runserver 0.0.0.0:8000
```

Visit [localhost:8000](http://localhost:8000)

### Create docker compose file to run multiple services (for the db)

Create `docker-compose.yml` with the following content.

```
version: '3'

services:
  db:
    image: postgres
    ports:
      - 5432:5432
  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/code
    ports:
      - "8000:8000"
    depends_on:
      - db
```

### Add database configuration in the settings file

Replace the `DATABASES` entry in `demo/settings.py` with the following.

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'postgres',
        'USER': 'postgres',
        'HOST': 'db',
        'PORT': 5432,
    }
}
```

### Run the server again

```bash
docker run -v ~/codes/dps/demo-docker:/code -p 8000:8000 demo python manage.py runserver 0.0.0.0:8000
```

Visit [localhost:8000](http://localhost:8000)

### Run django migrations to create db tables

```bash
docker-compose run web python manage.py makemigrations
docker-compose run web python manage.py migrate
```

### Create a superuser for the admin

```bash
docker-compose run web python manage.py createsuperuser
```

### Run the services using docker-compose

```bash
docker-compose up --build
```

## Some useful commands

```bash
docker <command>
```

- `ps` See running containers
- `ps -a` See all containers
- `start` containers
- `stop` containers
- `restart` containers
- `rm` Remove containers
- `images` See all images
- `rmi` Remove images
- `image prune` Remove intermediate layers to free up storage

