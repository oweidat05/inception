# Chapter 13 — TLS (HTTPS)

## Introduction

Imagine you open your browser and visit:

```
https://mowaidat.42.fr
```

A few seconds later,

your WordPress website appears.

It looks simple.

But before the page appears,

your browser and NGINX perform one of the most important security processes on the Internet.

This process is called:

**TLS**

Without TLS,

anyone on the network could read your passwords,

steal your cookies,

or modify the data sent between you and the server.

---

# What Is HTTP?

HTTP stands for:

```
HyperText Transfer Protocol
```

HTTP is simply a language.

It defines how browsers and web servers communicate.

Imagine two people speaking English.

```
Browser

↓

HTTP

↓

NGINX
```

Both understand the same language.

---

# The Problem With HTTP

HTTP sends everything as plain text.

Example.

```
Username = admin

Password = 123456
```

If someone is listening to the network,

they can read everything.

```
Browser
      │
      ▼
Internet

      ▼

😈 Hacker

Reads Password

      ▼

NGINX
```

Nothing is encrypted.

This is why HTTP is considered insecure.

---

# HTTPS

HTTPS means:

```
HTTP

+

TLS
```

Notice something.

HTTPS is **not a different protocol**.

It is simply:

```
HTTP

inside

TLS
```

TLS encrypts the HTTP messages before they travel across the network.

---

# What Is TLS?

TLS stands for:

```
Transport Layer Security
```

Its job is simple.

```
Plain Text

↓

Encrypt

↓

Send

↓

Decrypt

↓

Plain Text
```

Only the browser and the server can read the data.

Everyone else sees meaningless encrypted bytes.

---

# Encryption

Suppose you send this message.

```
Hello
```

Without TLS,

everyone sees:

```
Hello
```

With TLS,

everyone sees something like:

```
F83K2P91LQJ8...
```

Only the server knows how to convert it back.

---

# Why Do We Need A Certificate?

Encryption alone is not enough.

Imagine this.

Someone creates a fake website.

```
https://google.fake
```

How does your browser know it is not the real Google?

The answer is:

Certificates.

---

# What Is A Certificate?

A certificate is an identity card for a website.

Example.

```
Website

↓

Certificate

↓

Identity
```

It tells the browser:

- Who owns the website.
- Which domain it belongs to.
- Which public key should be used.

---

# Public Key And Private Key

When you created your certificate,

OpenSSL generated two files.

```
inception.crt

inception.key
```

These files are different.

---

## Public Key

```
inception.crt
```

Can be shared with everyone.

The browser downloads it during the TLS handshake.

---

## Private Key

```
inception.key
```

Must never leave the server.

Only NGINX owns it.

If someone steals the private key,

they can pretend to be your website.

---

# Certificate Authority (CA)

Anyone can create a certificate.

Example.

```
Mohammad

↓

Creates Certificate

↓

Signs It Himself
```

Would Firefox trust it?

No.

Firefox asks:

```
Who verified your identity?
```

The answer is:

Nobody.

---

Real websites ask a trusted company.

Examples:

- Let's Encrypt
- DigiCert
- GlobalSign
- Sectigo

These companies are called:

```
Certificate Authorities
```

They verify the website owner before signing the certificate.

Browsers already trust these companies.

---

# Self-Signed Certificate

Your project uses:

```
openssl req -x509
```

The option:

```
-x509
```

creates a self-signed certificate.

This means:

```
You

↓

Create Certificate

↓

Sign Certificate

↓

Use Certificate
```

Firefox says:

> I don't know who you are.

That is exactly why you saw this warning.

```
MOZILLA_PKIX_ERROR_SELF_SIGNED_CERT
```

Notice.

The connection is encrypted.

The browser simply cannot verify your identity.

---

# The TLS Handshake

Before any encrypted data is sent,

the browser and NGINX perform the TLS Handshake.

This is one of the most important processes on the Internet.

It happens in a few milliseconds.

---

## Step 1

The browser connects.

```
Browser

↓

NGINX
```

---

## Step 2

NGINX sends its certificate.

```
Browser

↓

Certificate

↓

NGINX
```

The browser now has:

- Domain name
- Public key
- Certificate signature

---

## Step 3

The browser verifies the certificate.

It asks:

- Is the certificate expired?
- Does the domain match?
- Is the certificate signed by a trusted Certificate Authority?

If the answer is yes,

the connection continues.

If not,

you see a warning.

---

## Step 4

The browser creates a random secret.

```
Random Secret
```

This will become the encryption key.

---

## Step 5

The browser encrypts the secret using the server's public key.

```
Random Secret

↓

Public Key

↓

Encrypted Secret
```

Only the server can decrypt it.

---

## Step 6

NGINX uses the private key.

```
Encrypted Secret

↓

Private Key

↓

Random Secret
```

Now both sides know the same secret.

Nobody else does.

---

## Step 7

Every HTTP message is now encrypted.

```
HTTP

↓

TLS Encryption

↓

Internet

↓

TLS Decryption

↓

HTTP
```

This is HTTPS.

---

# Why Did Firefox Show A Warning?

Your browser received:

```
inception.crt
```

Then Firefox asked:

```
Who signed this certificate?
```

The answer was:

```
You.
```

Firefox does not trust certificates signed by unknown people.

So it displayed:

```
MOZILLA_PKIX_ERROR_SELF_SIGNED_CERT
```

When you clicked:

```
Proceed
```

Firefox trusted your certificate temporarily.

The encrypted connection then started normally.

---

# Your Inception Project

This line in your Dockerfile creates the certificate.

```bash
openssl req -x509 \
-newkey rsa:2048 \
-keyout /etc/nginx/ssl/inception.key \
-out /etc/nginx/ssl/inception.crt
```

Later,

NGINX loads them.

```nginx
ssl_certificate     /etc/nginx/ssl/inception.crt;

ssl_certificate_key /etc/nginx/ssl/inception.key;
```

Now NGINX can perform the TLS handshake with every browser.

---

# HTTP vs HTTPS

HTTP

- No encryption
- Passwords travel as plain text
- Not secure

HTTPS

- Encrypted
- Uses TLS
- Uses certificates
- Protects passwords and cookies

---

# Key Points

TLS:

- encrypts communication
- protects passwords and cookies
- uses certificates
- uses public and private keys
- performs a TLS handshake before sending data

---
---
               Browser                              NGINX
                  │                                   │
                  │────── Client Hello ─────────────▶ │
                  │                                   │
                  │ ◀──── Certificate + Public Key ── │
                  │                                   │
                  │ Verify Certificate                │
                  │                                   │
                  │ Generate Random Secret            │
                  │                                   │
                  │ Encrypt Secret (Public Key)       │
                  │────── Encrypted Secret ─────────▶ │
                  │                                   │
                  │                    Decrypt (Private Key)
                  │                                   │
                  │◀══════════ Secure TLS Channel ═══▶│
                  │                                   │
                  │══════ Encrypted HTTP Request ═══▶ │
                  │◀══════ Encrypted HTTP Response ══ │
---
## Chapter Summary

After reading this chapter, you should be able to answer:

- What is HTTP?
- What is HTTPS?
- What is TLS?
- What is a certificate?
- What is a Certificate Authority?
- What is the difference between a public key and a private key?
- Why did Firefox display a self-signed certificate warning?
- What happens during the TLS handshake?

If you cannot answer these questions, read this chapter again before moving to the next chapter.

---

➡️ **Next:** Chapter 14 — WordPress
