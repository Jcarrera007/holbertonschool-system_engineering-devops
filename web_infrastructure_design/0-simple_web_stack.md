# 0. Simple Web Stack

## Infrastructure components

### What is a server?

A **server** is a computer (physical or virtual) that provides services to other computers over a network.

In this infrastructure, **one server** hosts:
- the web server
- the application server
- the application code
- the database

### What is the role of the domain name?

A **domain name** provides a human-readable way to access a website.

Instead of typing an IP address, users access the site using **www.foobar.com**, which maps to the server’s IP address.

### What type of DNS record is `www.foobar.com`?

`www.foobar.com` uses an **A record**.

An **A record** maps a domain name to an IPv4 address.

Example: `www.foobar.com → 8.8.8.8`

### What is the role of the web server?

The web server (**Nginx**):
- accepts incoming HTTP/HTTPS requests
- serves static content (HTML, CSS, images)
- forwards dynamic requests to the application server
- acts as a reverse proxy
- can handle SSL/TLS termination

### What is the role of the application server?

The application server:
- executes the backend application logic
- processes dynamic requests
- communicates with the database
- generates responses returned to the web server

### What is the role of the database?

The database (**MySQL**):
- stores persistent application data
- handles read/write operations
- ensures data consistency and durability
- responds to queries from the application server

### What does the server use to communicate with the user?

The server communicates with the user using:
- **HTTP** or **HTTPS**
- over the **TCP/IP** protocol
- typically on ports **80** (HTTP) or **443** (HTTPS)

## Issues with this infrastructure

### Single point of failure (SPOF)

All components run on a single server.

If the server fails, the entire website becomes unavailable.

### Downtime during maintenance

Deploying new code or updating services may require restarting:
- the web server
- the application server

This causes temporary downtime for users.

### Cannot scale with high traffic

The server has limited resources (CPU, RAM, disk).

Increased traffic can lead to:
- slow responses
- service crashes

No horizontal scaling or load balancing is possible.
