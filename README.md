# django_hello

!["It's dangerous to go alone! Take this."](zelda.jpg)

## Summary
Oftentimes the initial setup of a Python repo can take a few minutes to a couple hours.
By laying the foundation to rapidly implement an idea, can focus on the good bits instead of
devops drudgery.

## Setup
* Install 
    * [editorconfig](https://editorconfig.org/)
    * [poetry](https://python-poetry.org/docs/)
    * [docker-compose](https://docs.docker.com/compose/install/)
    * [playwright](https://playwright.dev/python/docs/intro#installation)

## Usage
### Poetry
```bash
# Install
curl -sSL https://install.python-poetry.org | $(which python3) -

# Change config
poetry config virtualenvs.in-project true           # .venv in `pwd`
poetry config experimental.new-installer false      # fixes JSONDecodeError on Python3.10

# Activate virtual environment (venv)
poetry shell

# Deactivate venv
exit  # ctrl-d

# Install multiple libraries
poetry add google-auth google-api-python-client

# Initialize existing project
poetry init

# Run script and exit environment
poetry run python your_script.py

# Install from requirements.txt
poetry add `cat requirements.txt`

# Update dependencies
poetry update

# Remove library
poetry remove google-auth

# Generate requirements.txt
poetry export -f requirements.txt --output requirements.txt --without-hashes
```

### Docker
```bash
# clean build (remove `--no-cache` for speed)
docker-compose build --no-cache --parallel

# start container
docker-compose up --remove-orphans -d

# exec into container
docker attach hello

# run command inside container
python hello.py

# destroy container
docker-compose down
```

#### Docker Troubleshooting
* Watch logs in real-time: `docker-compose logs -tf --tail="50" hello`
* Check exit code
    ```bash
    $ docker-compose ps
    Name                          Command               State    Ports
    ------------------------------------------------------------------------------
    docker_python      python manage.py runserver ...   Exit 0
    ```

### Playwright
```bash
# install
pip install --upgrade pip
pip install playwright
playwright install

# download new browsers (chromedriver, gecko)
npx playwright install

# generate code via macro
playwright codegen wikipedia.org
```

### Django
* Follow the official [Django Docker Compose article](https://docs.docker.com/samples/django/)
    * Django dependencies
        ```bash
        # edit requirements.txt
        Django>=3.0,<4.0
        psycopg2>=2.8
        ```
    * Replace the `compose.yml` and `Dockerfile`
        ```bash
        # compose.yml
        version: "3.9"
   
        services:
        db:
            image: postgres
            volumes:
            - ./data/db:/var/lib/postgresql/data
            environment:
            - POSTGRES_NAME=postgres
            - POSTGRES_USER=postgres
            - POSTGRES_PASSWORD=postgres
        web:
            build: .
            command: python manage.py runserver 0.0.0.0:8000
            volumes:
            - .:/code
            ports:
            - "8000:8000"
            environment:
            - POSTGRES_NAME=postgres
            - POSTGRES_USER=postgres
            - POSTGRES_PASSWORD=postgres
            depends_on:
            - db

        # Dockerfile
        # syntax=docker/dockerfile:1
        FROM python:3
        ENV PYTHONDONTWRITEBYTECODE=1
        ENV PYTHONUNBUFFERED=1
        WORKDIR /code
        COPY requirements.txt /code/
        RUN pip install -r requirements.txt
        COPY . /code/
        ```
    * Generate the server boilerplate code
        ```bash
        docker-compose run web django-admin startproject composeexample .
        ```
    * Fix upstream import bug and whitelist all hosts/localhost
        ```bash
        $ vim composeexample/settings.py
        import os
        ...
        ALLOWED_HOSTS = ["*"]
        ```
    * Profit
        ```bash
        docker-compose up
        ```

## TODO
* ~~Add boilerplate to hello.py~~
* ~~Poetry~~
* ~~Dockerfile~~
* ~~Playwright~~
* ~~Django~~
   * Merge with [docker_python](https://github.com/pythoninthegrass/docker_python) and put the latter on an ice float  
* Flask
* Terraform
* CI/CD (e.g., [Github Actions](https://docs.github.com/en/actions), [MegaLinter](https://megalinter.github.io/latest/))

## Further Reading
[Basic writing and formatting syntax - GitHub Docs](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)

[Introduction | Documentation | Poetry - Python dependency management and packaging made easy](https://python-poetry.org/docs/)

[Commands | Documentation | Poetry - Python dependency management and packaging made easy](https://python-poetry.org/docs/cli#export)

[Overview of Docker Compose | Docker Documentation](https://docs.docker.com/compose/)

[Compose file version 3 reference | Docker Documentation](https://docs.docker.com/compose/compose-file/compose-file-v3/)

[Getting started | Playwright Python | codegen macro](https://playwright.dev/python/docs/intro)
