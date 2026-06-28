# Chapter 11 — Docker Secrets

## Introduction

Imagine you are building a Docker image for MariaDB.

You need a database password.

Where should you put it?

Many beginners write something like this.

```dockerfile
ENV MYSQL_ROOT_PASSWORD=my_secret_password
```

Or even worse.

```dockerfile
RUN mysql -u root -pmy_secret_password
```

This works.

But it is a very bad idea.

Why?

Because anyone who downloads your image can discover your password.

Docker Secrets were created to solve this problem.

---

# What Is a Secret?

A secret is sensitive information.

Examples include:

- Passwords
- API Keys
- Private Keys
- Database Credentials
- Tokens

These values should never be stored inside your Docker image.

---

# Why Not Store Passwords In The Dockerfile?

Imagine this Dockerfile.

```dockerfile
ENV MYSQL_ROOT_PASSWORD=123456
```

Now build the image.

```
docker build
```

The password becomes part of the image.

Anyone who has the image can inspect it.

The password is no longer secret.

---

# Why Not Store Passwords In Git?

Imagine you upload this.

```
db_password.txt

123456
```

to GitHub.

Now every person in the world can read your password.

This is exactly why the Inception subject says:

> Never upload passwords to Git.

---

# The Better Solution

Instead,

store the password in a separate file.

Example.

```
db_password.txt

MySuperSecretPassword
```

Docker reads this file only when the container starts.

The password is never added to the image.

---

# Your Inception Project

Your project contains.

```
secrets/

├── db_password.txt
├── db_root_password.txt
├── wp_admin_password.txt
└── wp_user_password.txt
```

Each file stores one password.

Nothing else.

---

# docker-compose.yml

Your Compose file contains something like this.

```yaml
secrets:

  db_password:
    file: ../secrets/db_password.txt
```

Docker reads the file.

It creates a Docker Secret.

The container can now use it.

---

# Where Does Docker Put The Secret?

Docker mounts the secret inside the container.

Example.

```
/run/secrets/
```

Look inside the container.

```
/run/secrets/

├── db_password
├── db_root_password
├── wp_admin_password
└── wp_user_password
```

Notice something.

The extension `.txt` disappeared.

Docker only uses the secret name.

---

# Reading A Secret

Inside your setup.sh you wrote.

```bash
DB_PASSWORD=$(cat /run/secrets/db_password)
```

Let's explain every word.

```
cat
```

Read a file.

↓

```
/run/secrets/db_password
```

The secret file created by Docker.

↓

```
$(...)
```

Execute the command and return the output.

↓

```
DB_PASSWORD=
```

Store the result inside a shell variable.

Now the variable contains your database password.

---

# Why Is This Better?

The password exists only while the container is running.

It is not written inside the Dockerfile.

It is not baked into the image.

It can be changed without rebuilding the image.

---

# Can Someone Download My Password From Docker Hub?

Imagine you upload your image.

```
wordpress:inception
```

Someone downloads it.

They inspect every layer.

Will they find your password?

No.

Because the password was never stored inside the image.

Docker mounts it later when the container starts.

---

# Image vs Secret

Image

```
NGINX

PHP

MariaDB

WordPress
```

Secret

```
Password

API Key

Token
```

The image contains the application.

The secret contains confidential information.

They should always stay separate.

---

# Why Does Docker Mount Secrets As Files?

Docker could have used environment variables.

Instead,

Docker mounts them as files.

Example.

```
/run/secrets/db_password
```

Why?

Because many programs can read files directly.

Files are also easier to protect with Linux file permissions.

---

# Why /run/secrets?

```
/run
```

is a temporary directory used while the system is running.

Docker places secrets there because they are needed only while the container is running.

When the container stops,

the mounted secret disappears.

---

# Your Project

This is exactly what happens.

```
Host Computer

db_password.txt

        │

        ▼

Docker Secret

        │

        ▼

/run/secrets/db_password

        │

        ▼

setup.sh

        │

        ▼

MariaDB
```

The same happens for WordPress.

---

# Best Practices

Never:

- Put passwords inside Dockerfiles.
- Upload passwords to GitHub.
- Store passwords inside your images.

Always:

- Use Docker Secrets.
- Ignore secret files with `.gitignore`.
- Upload only `.example` files.

---

# Key Points

Docker Secrets:

- store confidential information
- are mounted when the container starts
- are not part of the image
- are read from `/run/secrets`
- make applications more secure

---

## Chapter Summary

After reading this chapter, you should be able to answer:

- What is a Docker Secret?
- Why not store passwords in Dockerfiles?
- Why not upload passwords to GitHub?
- Why are secrets stored in `/run/secrets`?
- How does `cat /run/secrets/db_password` work?
- Why are secrets more secure than environment variables?

If you cannot answer these questions, read this chapter again before moving to the next chapter.

---

➡️ **Next:** Chapter 12 — NGINX
