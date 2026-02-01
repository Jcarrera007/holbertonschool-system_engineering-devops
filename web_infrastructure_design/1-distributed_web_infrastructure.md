
# Distributed Web Infrastructure

This design extends a single-server setup into a **distributed** architecture by adding a **load balancer** and a **second application server**. The main goals are to improve capacity (handle more traffic) and increase availability (keep working if one server fails).

## High-level diagram

Some Markdown viewers (and some school checkers) do not render Mermaid diagrams. This ASCII diagram is compatible everywhere:

```text
User / Browser
	|
	| DNS: www.foobar.com
	v
Load Balancer (HAProxy)
	|\
	| \
	|  \
	v   v
Server 1 (Web + App)    Server 2 (Web + App)
	|        \              |
	|         \             |
	v          v            v
MySQL Primary (writes)   MySQL Replica (reads)
		   |
		   v
   Replication (one-way)
```

## Infrastructure components

### What was added (and why)

#### 1) Load balancer (HAProxy)

The load balancer sits in front of the web/app servers and **spreads incoming requests** across them. This helps:
- avoid overloading a single machine (reduces bottlenecks)
- increase throughput by using multiple servers
- keep the service available if one backend server goes down

#### 2) Server 1

Backend server that runs the web server + application server/application code.

If Server 1 fails, Server 2 can still answer requests (availability improves).

#### 3) Server 2

Second backend server identical in responsibility to Server 1.

If Server 2 fails, Server 1 can still answer requests (availability improves).

## Load balancer configuration

### Distribution algorithm: Round Robin

The load balancer uses **Round Robin**, which cycles through servers in order:

- request 1 → Server 1
- request 2 → Server 2
- request 3 → Server 1
- request 4 → Server 2

This approach is simple and works well when both servers have similar capacity.

### Active-Active vs Active-Passive

**Chosen setup: Active-Active**
- both Server 1 and Server 2 are online and handling traffic
- better overall capacity and resource usage

**Active-Passive (alternative)**
- only one server actively serves requests
- the second server stays idle and only takes over during failure
- simpler failover story, but wastes resources during normal operation

## Database cluster configuration

### How Primary–Replica (Master–Slave) works

In a **Primary–Replica** cluster:
- the **Primary** database handles all **writes** (INSERT/UPDATE/DELETE)
- the **Replica** database is used for **reads** (SELECT)
- data is copied from Primary to Replica via **one-way replication** (Primary → Replica)

### Primary vs Replica (from the application perspective)

- **Primary** is the source of truth for new/updated data

  - the application connects here to write data
- **Replica** follows the Primary

  - the application can connect here to offload read traffic
  - replication is not bi-directional; changes originate on the Primary

## Infrastructure issues

### 1) Single points of failure (SPOF)

Even with two backend servers, some critical components are still single:
- **Load balancer**: if it fails, users can’t reach either server
- **Primary database**: if it fails, the application can’t perform writes (site may become read-only or break)

### 2) Security gaps (no firewall, no HTTPS)

- **No firewall**: services/ports may be exposed directly to the internet, increasing attack surface
- **No HTTPS**: traffic is unencrypted, which allows interception/tampering on the network

### 3) No monitoring

Without monitoring:
- there’s no visibility into performance (CPU, RAM, latency, errors)
- it’s harder to detect failing servers early
- there are no alerts when incidents happen
