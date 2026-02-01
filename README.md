# Web Stack Infrastructure Overview

This repository documents a web stack infrastructure built as part of the **SysAdmin / DevOps** track projects. The goal is to demonstrate a clear understanding of how a typical web stack is structured, how each component functions, and how system reliability and performance are achieved.

## Contents

- [Project files](#project-files)
- [Web stack diagram (concept)](#web-stack-diagram-concept)
- [Components](#components)
- [System redundancy](#system-redundancy)
- [Acronyms](#acronyms)
- [Objective](#objective)

## Project files

- [web_infrastructure_design/0-simple_web_stack.md](web_infrastructure_design/0-simple_web_stack.md)
- [web_infrastructure_design/1-distributed_web_infrastructure.md](web_infrastructure_design/1-distributed_web_infrastructure.md)

## Web stack diagram (concept)

The web stack design can be represented through an infrastructure diagram that illustrates how all components interact to deliver a web application to end users.

Core elements:
- **User** (client browser)
- **DNS**
- **Web server**
- **Application server**
- **Database server**

The diagram shows the flow of requests from the user through the web stack and back with a response.

## Components

### User (client)

The user accesses the application through a web browser by entering a domain name. The browser sends HTTP/HTTPS requests to the server hosting the application.

### DNS (Domain Name System)

DNS translates the domain name into an IP address so the client can locate the server hosting the web application.

### Web server

The web server (e.g., Apache or Nginx) handles incoming HTTP requests. It serves static content and forwards dynamic requests to the application server.

### Application server

The application server processes business logic, executes application code, and communicates with the database when needed.

### Database server

The database server (e.g., MySQL) stores and retrieves application data such as user information, settings, and content.

## System redundancy

System redundancy is the practice of duplicating critical components to avoid a **Single Point of Failure (SPOF)**.

Examples of redundancy:
- multiple web servers behind a load balancer
- replicated databases
- backup servers and failover mechanisms

Redundancy improves:
- availability
- reliability
- fault tolerance

Without redundancy, the failure of a single component can bring down the entire system.

## Acronyms

### LAMP

LAMP is a common web stack composed of:
- **Linux** — Operating system
- **Apache** — Web server
- **MySQL** — Database
- **PHP / Python / Perl** — Application layer

### SPOF

**SPOF (Single Point of Failure)** refers to any component whose failure would cause the entire system to stop functioning.

### QPS

**QPS (Queries Per Second)** measures how many requests or queries a system can handle per second. It is commonly used to evaluate system performance and scalability.

## Objective

By completing this project, you should be able to:
- draw and explain a complete web stack infrastructure
- describe the role of each system component
- identify and explain system redundancy
- understand and use common infrastructure-related acronyms