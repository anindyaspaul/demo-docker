# Demo

_You might need to add sudo before the docker commands._

_Instead of vim, you can use text editor of your choice._

### Prerequisites

Install `docker` and `docker-compose` so that you can see the version names from the following commands.

```bash
docker -v
docker-compose -v
```

### Create project directory

```bash
mkdir demo-docker
cd demo-docker
```

### List django dependencies

```
vim requirements.txt
```

Enter the following.

```
Django
psycopg2
```

### Create Dockerfile

```bash
vim Dockerfile
```

Enter the following.

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

### Run the project and visit 127.0.0.0:8000

```bash
docker run -v ~/codes/dps/demo-docker:/code -p 8000:8000 demo python manage.py runserver 0.0.0.0:8000
```

### Create docker compose file to run multiple services (for the db)

```bash
vim docker-compose.yml
```

Enter the following.

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

```bash
sudo vim demo/settings.py
```

Replace the database entry with the following.

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

### Run the server again and visit 127.0.0.1:8000

```bash
docker run -v ~/codes/dps/demo-docker:/code -p 8000:8000 demo python manage.py runserver 0.0.0.0:8000
```

### Run the services using docker-compose

```bash
docker-compose up --build
```

docker-compose run web python manage.py makemigrations

docker-compose run web python manage.py migrate

docker-compose run web python manage.py createsuperuser

docker ps

psql -h localhost -U postgres
\dt
select * from auth_user;
\q

