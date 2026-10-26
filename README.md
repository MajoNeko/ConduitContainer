# Conduit Container

Sample project to containerize a fullstack web application, consisting of a backend in django and the respective client in angular.

## Table of Contents

- [Conduit Container](#conduit-container)
- [Prerequisites](#prerequisites)
- [Known Issues](#known-issues)
- [Quickstart](#quickstart)
- [Project Structure](#project-structure)
- [Usage](#usage)
  - [Steps and Configuration](#steps-and-configuration)
  - [Modifying Behavior](#modifying-behavior)
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

Django 1.10.5 (released 2017) only supports Python 2.7, 3.4 and 3.5, and does not run on modern Python releases. Matching the container base image to the dependencies (python:3.6-slim) is what allows the application to run unmodified. The container works as intended, but the runtime receives no security patches.

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
docker ps
```

5. Your Angular frontend will now be reachable under: <CLOUD_VM_IP>:<FRONTEND_PORT> and you can complete the fullstack website setup by acessing the Django backend <CLOUD_VM_IP>:<BACKEND_PORT>/admin.

## Project Structure

- ".docs" : Contains any relevant documents (Project checklist).
- "gitignore" : Prevents irrelevant files from being commited.
- "docker-compose-yaml" : A configuration file for Docker to define the necessary services such as wordpress image, mysql db image, port mapping, volumes, etc.
- "example.env" : Contains example environment variables to setup the project such as database cedentials and non critical configuration values kept out of the Compose file.
- "README.md" : This document. Contains insructions on how to setup and deploy the containers (Wordpress and DB).
- "backend/conduit-backend" : Contains all necessary files to build the Django backend, including the Dockerfile.
- "fronted/conduit-frontend" : Contains all necessary files to build the Angular frontend, including the Dockerfile.

## Usage

### Steps and Configuration

1. Clone the repository.

### Modifying Behavior

- #### Reset everything

    docker compose down -v removes the containers and both volumes, giving you a clean install. This deletes all site data — use it only when you intend to start over.
