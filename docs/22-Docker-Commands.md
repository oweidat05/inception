# Chapter 22 — Docker Commands

## Introduction

Docker has many commands.

Some commands manage images.

Some manage containers.

Some manage networks.

Some manage volumes.

In this chapter, we will learn the Docker commands used throughout the Inception project.

Each command will answer four questions:

- What does it do?
- When should I use it?
- What do the words mean?
- Example from the Inception project.

---

# Docker Command Categories

Docker commands can be grouped into five categories.

```
Docker
│
├── Images
├── Containers
├── Networks
├── Volumes
└── Docker Compose
```

---

# Image Commands

## docker build

```bash
docker build -t wordpress .
```

### What does it do?

Builds a Docker image from a Dockerfile.

### Word by Word

```
docker
```

The Docker program.

```
build
```

Build an image.

```
-t
```

Tag.

Give the image a name.

```
wordpress
```

The image name.

```
.
```

Current directory.

Docker searches for the Dockerfile in this directory.

### Example

```
Dockerfile

↓

docker build

↓

Docker Image
```

---

## docker images

```bash
docker images
```

Shows all images stored on your computer.

Example:

```
REPOSITORY      TAG

wordpress       inception

nginx           inception

mariadb         inception
```

---

## docker pull

```bash
docker pull nginx
```

Downloads an image from Docker Hub.

Example:

```
Docker Hub

↓

Download

↓

Local Image
```

---

## docker image rm

```bash
docker image rm nginx
```

Deletes an image from your computer.

The container must not be using this image.

---

# Container Commands

## docker run

```bash
docker run nginx
```

Creates and starts a container.

Internally Docker performs:

```
Find Image

↓

Create Container

↓

Start Container
```

If the image does not exist:

```
Docker Hub

↓

Download Image

↓

Create Container

↓

Start Container
```

---

## docker ps

```bash
docker ps
```

Shows running containers.

---

## docker ps -a

```bash
docker ps -a
```

Shows every container.

Running and stopped.

---

## docker exec

```bash
docker exec -it wordpress sh
```

Execute a command inside a running container.

### Word by Word

```
exec
```

Execute.

```
-i
```

Interactive.

```
-t
```

Terminal.

```
wordpress
```

Container name.

```
sh
```

Shell.

---

## docker logs

```bash
docker logs wordpress
```

Shows everything printed by the container.

Follow logs in real time:

```bash
docker logs -f wordpress
```

---

## docker inspect

```bash
docker inspect wordpress
```

Shows detailed information about the container.

Examples:

- IP Address
- Networks
- Mounts
- Volumes
- Environment Variables

---

## docker stop

```bash
docker stop wordpress
```

Stops a running container.

---

## docker start

```bash
docker start wordpress
```

Starts an existing stopped container.

---

## docker restart

```bash
docker restart wordpress
```

Stops and starts the container again.

---

## docker rm

```bash
docker rm wordpress
```

Deletes a stopped container.

---

# Network Commands

## docker network ls

```bash
docker network ls
```

Lists all Docker networks.

---

## docker network inspect

```bash
docker network inspect inception
```

Shows every container connected to a network.

---

## docker network create

```bash
docker network create inception
```

Creates a new Docker network.

---

## docker network rm

```bash
docker network rm inception
```

Deletes a Docker network.

---

# Volume Commands

## docker volume ls

```bash
docker volume ls
```

Lists all Docker volumes.

---

## docker volume inspect

```bash
docker volume inspect wordpress_data
```

Shows where Docker stores the volume.

---

## docker volume create

```bash
docker volume create wordpress_data
```

Creates a new volume.

---

## docker volume rm

```bash
docker volume rm wordpress_data
```

Deletes a volume.

---

# Docker Compose Commands

## docker compose up

```bash
docker compose up
```

Creates and starts every service defined in docker-compose.yml.

---

## docker compose up -d

```bash
docker compose up -d
```

Starts containers in detached mode.

Your terminal remains available.

---

## docker compose up --build

```bash
docker compose up --build
```

Rebuilds images before starting containers.

---

## docker compose down

```bash
docker compose down
```

Stops and removes containers, networks, and temporary resources created by Docker Compose.

---

## docker compose build

```bash
docker compose build
```

Builds all images without starting containers.

---

## docker compose ps

```bash
docker compose ps
```

Shows every container managed by Docker Compose.

---

## docker compose logs

```bash
docker compose logs
```

Shows logs from every service.

Specific service:

```bash
docker compose logs nginx
```

Real-time logs:

```bash
docker compose logs -f wordpress
```

---

## docker compose exec

```bash
docker compose exec wordpress sh
```

Executes a command inside a running service.

---

## docker compose restart

```bash
docker compose restart
```

Restarts every service.

---

## docker compose stop

```bash
docker compose stop
```

Stops services without removing them.

---

## docker compose start

```bash
docker compose start
```

Starts previously stopped services.

---

# Cleaning Commands

## docker system prune

```bash
docker system prune
```

Removes unused Docker resources.

---

## docker system prune -a

```bash
docker system prune -a
```

Also removes unused images.

---

## docker image prune

```bash
docker image prune
```

Removes unused images.

---

## docker container prune

```bash
docker container prune
```

Removes stopped containers.

---

## docker volume prune

```bash
docker volume prune
```

Removes unused volumes.

---

## docker network prune

```bash
docker network prune
```

Removes unused networks.

---

# Most Useful Commands for Inception

```bash
docker compose up --build

docker compose down

docker compose ps

docker compose logs -f

docker exec -it wordpress sh

docker exec -it mariadb sh

docker ps

docker images

docker inspect wordpress

docker network inspect inception

docker volume inspect wordpress_data

docker stats
```

---

# Chapter Summary

After reading this chapter, you should be able to answer:

- How do I build an image?
- How do I create a container?
- How do I enter a running container?
- How do I view logs?
- How do I inspect networks?
- How do I inspect volumes?
- How do I rebuild the project?
- How do I remove unused Docker resources?
