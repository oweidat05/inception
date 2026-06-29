# Chapter 23 — Docker Debugging

## Introduction

Building Docker containers is only half of the job.

The other half is debugging problems when something does not work.

Every Docker developer spends a large amount of time finding and fixing problems.

In this chapter, we will learn how to debug Docker containers using the commands that are most useful during the Inception project.

---

# Debugging Strategy

Whenever something is wrong, always ask these questions in order.

```
Is the container running?

↓

Did the application crash?

↓

Can containers communicate?

↓

Are the volumes mounted?

↓

Is the configuration correct?

↓

Can I reproduce the problem inside the container?
```

Never guess.

Always collect information first.

---

# Step 1 — Is The Container Running?

The first command should always be:

```bash
docker compose ps
```

Example:

```
NAME          STATUS

nginx         Up

wordpress     Up

mariadb       Up
```

If one container is not running,

nothing else matters.

Fix that first.

---

# Step 2 — Read The Logs

If a container stopped,

read its logs.

```bash
docker compose logs
```

Or inspect only one service.

```bash
docker compose logs nginx

docker compose logs wordpress

docker compose logs mariadb
```

Follow logs in real time.

```bash
docker compose logs -f wordpress
```

Logs usually tell you exactly why the container stopped.

---

# Step 3 — Enter The Container

Sometimes logs are not enough.

Enter the container.

```bash
docker exec -it nginx sh

docker exec -it wordpress sh

docker exec -it mariadb sh
```

Now you are inside the Linux environment.

You can inspect files and execute commands.

---

# Step 4 — Check Running Processes

Inside the container run:

```bash
ps aux
```

Example:

```
PID

php-fpm

nginx

mariadbd
```

Question:

Is the application actually running?

If not,

find out why.

---

# Step 5 — Check Configuration Files

Sometimes the application is running,

but the configuration is wrong.

Examples.

NGINX

```bash
cat /etc/nginx/conf.d/default.conf
```

WordPress

```bash
cat /var/www/html/wp-config.php
```

PHP-FPM

```bash
cat /etc/php/8.2/fpm/pool.d/www.conf
```

MariaDB

```bash
cat /etc/mysql/mariadb.conf.d/50-server.cnf
```

---

# Step 6 — Test Configuration

NGINX can test its configuration.

```bash
nginx -t
```

Output:

```
syntax is ok

test is successful
```

If this command fails,

NGINX will never start.

---

PHP-FPM can also test its configuration.

```bash
php-fpm8.2 -t
```

---

# Step 7 — Check Network

Can containers communicate?

Inspect the network.

```bash
docker network inspect inception
```

Look for:

```
Containers
```

Example:

```
nginx

wordpress

mariadb
```

If one container is missing,

it cannot communicate.

---

# Step 8 — Test Connection

From the WordPress container.

```bash
docker exec -it wordpress sh
```

Try to reach MariaDB.

```bash
ping mariadb
```

Or

```bash
getent hosts mariadb
```

If Docker DNS works,

you should receive an IP address.

---

# Step 9 — Check Volumes

List all volumes.

```bash
docker volume ls
```

Inspect one volume.

```bash
docker volume inspect wordpress_data
```

Check:

```
Mountpoint
```

This shows where Docker stores the data.

---

# Step 10 — Inspect Container

One of the most useful commands.

```bash
docker inspect wordpress
```

Shows:

- Networks
- Volumes
- Environment Variables
- Mounts
- Ports
- Restart Policy

Almost everything Docker knows about the container.

---

# Step 11 — Monitor Resources

```bash
docker stats
```

Example:

```
CPU

Memory

Network

Block I/O
```

This helps detect:

- High CPU usage
- Memory problems
- Resource limits

---

# Common Problems

## NGINX Returns 502 Bad Gateway

Possible causes:

- PHP-FPM is not running.
- Wrong FastCGI configuration.
- Wrong port.
- Wrong container name.

Commands:

```bash
docker compose logs wordpress

ps aux

php-fpm8.2 -t
```

---

## WordPress Cannot Connect To MariaDB

Possible causes:

- MariaDB stopped.
- Wrong password.
- Wrong host.
- Wrong database.
- Wrong Docker network.

Commands:

```bash
docker compose logs mariadb

cat wp-config.php

docker network inspect inception
```

---

## MariaDB Refuses Connection

Possible causes:

- Wrong bind-address.
- Wrong user.
- Wrong password.
- Database not initialized.

Commands:

```bash
docker compose logs mariadb

cat /etc/mysql/mariadb.conf.d/50-server.cnf

mariadb -u root -p
```

---

## Changes Do Not Appear

Possible causes:

- Docker cache.
- Old image.
- Volume still contains old data.

Try rebuilding.

```bash
docker compose down

docker compose up --build
```

If necessary,

remove unused resources.

```bash
docker system prune -a
```

---

# Debugging Checklist

Whenever Docker is broken:

```
✓ Is the container running?

✓ Read logs.

✓ Enter the container.

✓ Check running processes.

✓ Check configuration files.

✓ Test configuration.

✓ Inspect the network.

✓ Inspect the volume.

✓ Inspect the container.

✓ Monitor resources.
```

---

# Key Points

Good debugging is not guessing.

Good debugging means collecting information step by step.

Always start with:

```
docker compose ps
```

Then continue using logs,

configuration files,

and inspection commands until you find the real problem.

---

# Chapter Summary

After reading this chapter, you should be able to answer:

- How do I know if a container is running?
- How do I read logs?
- How do I enter a container?
- How do I inspect a Docker network?
- How do I inspect a Docker volume?
- How do I debug WordPress?
- How do I debug MariaDB?
- How do I debug NGINX?
