# Conduit Container

Sample project to containerize a fullstack web application, consisting of a backend in django and the respective client in angular.

## Table of Contents

- [Conduit Container](#conduit-container)
- [Prerequisites](#prerequisites)
- [Quickstart](#quickstart)
- [Project Structure](#project-structure)
- [Usage](#usage)
  - [Steps and Configuration](#steps-and-configuration)
  - [Modifying Behavior](#modifying-behavior)
    - [Reset everything](#reset-everything)

## Prerequisites

Docker engine and Docker compose must be installed on the host.

## Quickstart

1. Create .env file

```bash
cp example.env .env 
```

2. Setup your user name and password for both worpress and the mysql db. Make sure they match.

3. Start the services:

```bash
docker compose up -d
```

4. Verify the containers are running:

```bash
docker ps
```

5. Your wordpress will now be reachable under: <CLOUD_VM_IP>:<SERVER_PORT> and you can complete the Wordpress instalation.

## Project Structure

- ".docs" : Contains any relevant documents (Project checklist).
- "gitignore" : Prevents irrelevant files from being commited.
- "docker-compose-yaml" : A configuration file for Docker to define the necessary services such as wordpress image, mysql db image, port mapping, volumes, etc.
- "example.env" : Contains example environment variables to setup the project such as database cedentials and non critical configuration values kept out of the Compose file.
- "README.md" : This document. Contains insructions on how to setup and deploy the containers (Wordpress and DB).

## Usage

### Steps and Configuration

1. Clone the repository.

### Modifying Behavior

- #### Reset everything

    docker compose down -v removes the containers and both volumes, giving you a clean install. This deletes all site data — use it only when you intend to start over.
