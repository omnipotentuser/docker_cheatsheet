# docker_cheatsheet
Things I picked up learning about Docker

# creating a project with docker-compose
```
~/develop/docker-app
                   |- app
                   |- config
                   |- data
```

The app code goes in app.

The config that's mounted into containers via volumes goes in config.

Any data that's also mounted into containers via volumes goes into data.

Place Dockerfile and Dockerfile.deploy into app as well.

The reason for two Dockerfiles is because the deploy one is built differently cuz prod doesn't need a bunch of dev stuff.

Place docker-compose.yml in the docker-app dir.

sample docker-compose.yml
```
version: '3.7'

services:

  app:
    image: scomm:cif
    build:
      context: ./app
      dockerfile: Dockerfile
    working_dir: /app
    volumes:
      - ./app:/app
      - ./config/bashrc:/app/.bashrc
    ports:
      - "4003:4001"
    env_file: config/app.env
    tty: true
    stdin_open: true
```
sample Dockerfile
```
FROM elixir:1.11-slim

ENV MIX_HOME=/opt/mix

RUN useradd --home-dir /app --create-home --user-group app \
  && apt-get update \
  && apt-get install -y \
    apt-utils \
    build-essential \
    inotify-tools \
    npm \
  && rm -rf /var/lib/apt/lists/* \
  && chown -R app:app /app \
# Install Hex, Rebar, and Phoenix \
  && mix local.hex --force \
  && mix local.rebar --force \
  && mix archive.install hex phx_new --force

USER app
WORKDIR /app

CMD ["bash"]
```
To start it all up, go into the app's root (~/develop/docker-app) and run `docker-compose up -d` which starts up all containers within the docker-compose file.

to get ID of process, run `docker ps`

`docker exec -it <container pid> bash`

`> iex -S mix phx.server`

web browser go to the port :4003 or whats set in docker-compose.yml

to build the container,
`docker-compose build app`

For more reference,
https://docs.docker.com/compose/install/
  
