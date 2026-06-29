# Chapter 21 — Complete Request Life Cycle

## Introduction

Throughout this guide, we learned about:

- Docker
- Containers
- Networks
- Volumes
- NGINX
- TLS
- PHP
- PHP-FPM
- WordPress
- MariaDB
- SQL

Now it's time to connect everything together.

In this chapter, we will follow **one browser request** from the moment the user presses **Enter** until the webpage appears on the screen.

---

# Step 1 — The User Enters the URL

The user types:

```
https://mowaidat.42.fr
```

and presses **Enter**.

```
User

↓

Browser
```

At this moment, the browser only knows the domain name.

It does not know where the server is.

---

# Step 2 — DNS Resolution

The browser asks DNS:

```
Where is mowaidat.42.fr?
```

DNS replies with an IP address.

Example:

```
192.168.1.50
```

Now the browser knows where to connect.

---

# Step 3 — TCP Connection

The browser creates a TCP connection.

```
Browser

↓

Server IP

↓

Port 443
```

Port **443** is the default port for HTTPS.

---

# Step 4 — TLS Handshake

Before sending any HTTP request,

the browser creates a secure connection.

NGINX sends:

```
inception.crt
```

The browser verifies the certificate.

If it is valid,

both sides create a shared secret.

From now on,

all communication is encrypted.

```
Browser

⇄

TLS

⇄

NGINX
```

---

# Step 5 — HTTP Request

Now the browser sends:

```http
GET /
```

to NGINX.

```
Browser

↓

HTTPS GET /

↓

NGINX
```

---

# Step 6 — NGINX Receives the Request

NGINX receives:

```
GET /
```

It reads:

```
nginx.conf
```

Then executes:

```nginx
try_files $uri $uri/ /index.php?$args;
```

NGINX checks:

```
Does the requested file exist?
```

No.

So it decides to execute:

```
index.php
```

---

# Step 7 — FastCGI

NGINX cannot execute PHP.

Instead,

it sends the request to PHP-FPM.

```
NGINX

↓

FastCGI

↓

wordpress:9000
```

FastCGI is simply the protocol that NGINX uses to communicate with PHP-FPM.

---

# Step 8 — PHP-FPM

PHP-FPM receives:

```
index.php
```

It starts executing the PHP code.

```
PHP-FPM

↓

Execute PHP

↓

WordPress
```

---

# Step 9 — WordPress Starts

WordPress loads many PHP files.

Eventually,

it loads:

```
wp-config.php
```

This file contains:

- Database host
- Database name
- Database user
- Database password

Now WordPress knows how to connect to MariaDB.

---

# Step 10 — Connect to MariaDB

WordPress executes PHP code similar to:

```php
mysqli_connect(
    "mariadb",
    "mowaidat",
    "password",
    "wordpress"
);
```

Notice.

WordPress connects to MariaDB.

Not PHP-FPM.

PHP-FPM only executes the PHP code.

---

# Step 11 — SQL Query

WordPress needs the latest posts.

It sends SQL similar to:

```sql
SELECT *

FROM wp_posts

ORDER BY post_date DESC

LIMIT 10;
```

MariaDB executes the query.

---

# Step 12 — MariaDB Returns the Data

MariaDB searches the table:

```
wp_posts
```

Then returns the rows.

Example:

```
Post 1

Post 2

Post 3
```

---

# Step 13 — WordPress Builds HTML

Now WordPress has the data.

It combines:

- Theme
- Posts
- Menu
- Images
- Header
- Footer

It generates HTML.

Example:

```html
<h1>Welcome</h1>

<p>Hello World</p>
```

This HTML did not exist before.

WordPress created it.

---

# Step 14 — PHP-FPM Returns the Result

PHP-FPM finishes executing the PHP code.

It returns the generated HTML to NGINX.

```
PHP-FPM

↓

HTML

↓

NGINX
```

---

# Step 15 — NGINX Sends the Response

NGINX creates an HTTP response.

Example:

```http
HTTP/1.1 200 OK

Content-Type: text/html
```

Then sends the HTML through the encrypted TLS connection.

---

# Step 16 — Browser Displays the Website

The browser receives:

- HTML
- CSS
- JavaScript

It renders the page.

The user finally sees:

```
https://mowaidat.42.fr
```

Everything happened in only a few milliseconds.

---

# Complete Flow

```
User
        │
        ▼
Browser
        │
        ▼
DNS
        │
        ▼
Server IP
        │
        ▼
TCP Connection
        │
        ▼
TLS Handshake
        │
        ▼
HTTPS GET /
        │
        ▼
NGINX
        │
        ▼
try_files
        │
        ▼
index.php
        │
        ▼
FastCGI
        │
        ▼
PHP-FPM
        │
        ▼
Execute WordPress
        │
        ▼
wp-config.php
        │
        ▼
MariaDB Connection
        │
        ▼
SQL Query
        │
        ▼
MariaDB
        │
        ▼
Return Data
        │
        ▼
WordPress
        │
        ▼
Generate HTML
        │
        ▼
PHP-FPM
        │
        ▼
NGINX
        │
        ▼
HTTPS Response
        │
        ▼
Browser
        │
        ▼
Website Appears
```

---

# Everything We Learned

| Component | Responsibility |
|-----------|----------------|
| Docker | Runs isolated containers |
| Docker Compose | Starts all containers together |
| NGINX | Receives browser requests |
| TLS | Encrypts communication |
| FastCGI | Connects NGINX to PHP-FPM |
| PHP-FPM | Executes PHP code |
| WordPress | Generates HTML |
| wp-config.php | Stores database credentials |
| MariaDB | Stores website data |
| SQL | Language used to communicate with MariaDB |

---

# Final Summary

The browser never talks directly to:

- PHP-FPM
- WordPress
- MariaDB

The communication chain is always:

```
Browser

↓

NGINX

↓

PHP-FPM

↓

WordPress

↓

MariaDB
```

Then the response travels back through the same path until it reaches the browser.

Understanding this complete flow means you understand how your entire Inception project works.
