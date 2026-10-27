# Conduit Container

Sample project to containerize a fullstack web application, consisting of a backend in django and the respective client in angular.

## Table of Contents

- [Conduit Container](#conduit-container)
- [Prerequisites](#prerequisites)
- [Known Issues](#known-issues)
- [Quickstart](#quickstart)
- [Project Structure](#project-structure)
- [Usage](#usage)
  - [Networking](#networking)
  - [Multi-Staging](#multi-staging)
  - [Steps and Configuration](#steps-and-configuration)
  - [Modifying Behavior](#modifying-behavior)
    - [Enable debug mode](#enable-debug-mode)
    - [Change access ports](#change-access-ports)
    - [Reset everything](#reset-everything)

## Prerequisites

Docker engine and Docker compose must be installed on the host.

## Known Issues

### End-of-life Python runtime (inherited from upstream)

The backend is pinned to an end-of-life Python runtime (3.6), which reached EOL in December 2021 and no longer receives security updates.
The application's dependencies are fixed by the upstream repository and require an old interpreter:

```bash
Django==1.10.5
django-cors-middleware==1.3.1
django-extensions==1.7.1
djangorestframework==3.4.4
PyJWT==1.4.2
six==1.10.0
```

Django 1.10.5 (released 2017) only supports Python 2.7, 3.4 and 3.5 (though it runs on 3.6 with warnings), and does not run on modern Python releases. Matching the container base image to the dependencies (python:3.6-slim) is what allows the application to run unmodified. The container works as intended, but the runtime receives no security patches.

## Quickstart

1. Create .env file

```bash
cp example.env .env 
```

2. Setup your user name and password for both the backend and the postgres db. Make sure they match.

3. Start the services:

```bash
docker compose up -d
```

4. Verify the containers are running:

```bash
docker compose ps
```

5. Your Angular frontend will now be reachable under: <localhost>:<FRONTEND_PORT> and you can complete the fullstack website setup by accessing the Django backend <localhost>:<BACKEND_PORT>/admin.

## Project Structure

- ".docs" : Contains any relevant documents (Project checklist).
- ".gitignore" : Prevents irrelevant files from being committed.
- "docker-compose.yaml" : A configuration file for Docker to define the necessary services such as the backend build, postgres db image, frontened build, port mapping, volumes, etc.
- "example.env" : Contains example environment variables to setup the project such as database credentials and non critical configuration values kept out of the Compose file.
- "README.md" : This document. Contains instructions on how to setup and deploy the containers (backend, frontend and DB).
- "backend/conduit-backend" : Contains all necessary files to build the Django backend, including the Dockerfile.
- "fronted/conduit-frontend" : Contains all necessary files to build the Angular frontend, including the Dockerfile.

## Usage

There are three services declared: a Django backend, an Angular frontend and a Postgres db. The backend depends on the db so Compose starts the db first, then the backend and finally the frontend which depends on the backend. 

### Networking

The three services communicate over Docker Compose default bridge network. The backend service connects to the db by the servicename (received from the .env and registered as a DNS entry by Compose) instead of an IP address. The angluar application is built with a relative API base path, so every request goes to the same origin the page was served from. The Dockerfile then rewrites the upstream URL in the API interceptor and nginx server then serves a static bundle which is proxied under /api/ to the backend container.

### Multi-Staging

The frontend Dockerfile uses two stages. Only the second one ends up in the final image. Stage 1 compiles the Angular application using the full node tool chain, npm, the Angular CLI, etc. Only the output matters for the runtime. Stage 2 will start nginx alpine and copies only the compiled bundle and the nginx config file. This reduces the size of the final image significantly. 
This two step stage process is not truly necessary for the backend as python needs its installed packages at runtime so it wouldn't really save much by having a multi stage setup for the backend.

### Steps and Configuration

1. Clone the repository.

```bash
git clone <https://github.com/MajoNeko/ConduitContainer>
```

2. Copy the environment variables and set up your username and password for both the backend and the postgres db. The credentials must match to avoid an "Error establishing a database connection" as the database creates the user and the backend then authenticates with it.

```bash
cp example.env .env 
```

3. Build and start the services by running the following command:

```bash
docker compose up -d --build
```

This will take a while the first time.

4. To check if it started correctly use the following command:

```bash
docker compose ps
```

5. Access the frontend through <localhost>:<8282>

6. (Optional)  You can inspect logs by using the following commands:

#### follow all services

```bash
docker compose logs -f
```

#### single service, last 100 lines with timestamps

```bash
docker compose logs --tail=100 -t backend
```

#### Persist logs to a file for later use:

```bash
mkdir -p logs
docker compose logs --no-color --timestamps [container] > logs/compose[container].log
(or you can use: docker logs [container-name] > logs/file.txt )
```

### Modifying Behavior

- #### Enable debug mode

    Set DJANGO_DEBUG=1 in the .env file, then restart.

- #### Change access ports

  You can change the frontend or backend access ports by altering the value under FRONTEND_PORT or BACKEND_PORT in the .env file.

- #### Reset everything

    Docker compose down -v removes the containers and db volume, giving you a clean install. This deletes all site data — use it only when you intend to start over.
