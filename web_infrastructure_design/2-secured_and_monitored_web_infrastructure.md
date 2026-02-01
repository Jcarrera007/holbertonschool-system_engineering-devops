
# Secured and Monitored Web Infrastructure

This design builds on a distributed setup by adding **security controls** (firewalls + HTTPS) and **observability** (monitoring). The goal is to reduce risk while making the system easier to operate and troubleshoot.

## Additional elements added (and why)

Compared to a basic distributed setup (load balancer + multiple servers), this version adds:

- **Firewalls**: restrict inbound/outbound traffic so only required ports/services are reachable.
- **HTTPS (TLS certificates + configuration)**: encrypt user traffic and provide authenticity/integrity.
- **Monitoring system + monitoring agents**: collect metrics/logs so you can detect issues early, track performance, and alert on incidents.

## High-level diagram (ASCII)

```text
User / Browser
	|
	| HTTPS
	v
Load Balancer (HAProxy)
	|
	| (internal traffic)
	v
   [Firewall rules]
	|
	+--> Server 1 (Web + App)  -- Monitoring Agent --> Monitoring System
	|
	+--> Server 2 (Web + App)  -- Monitoring Agent --> Monitoring System
	|
	+--> Database (MySQL)      -- Monitoring Agent --> Monitoring System
```

## Infrastructure components

### What are firewalls for?

A **firewall** filters traffic to/from a server or network.

In practice, it:
- inspects incoming/outgoing network packets
- allows or blocks traffic based on rules (IP, port, protocol, direction)
- reduces exposure by only permitting what the service actually needs

### Why is the traffic served over HTTPS?

Serving traffic over **HTTPS** matters because it provides:
- **encryption**: protects data in transit from being read by third parties
- **authentication**: the certificate helps users verify they are talking to the correct site
- **integrity**: reduces the risk of traffic being altered in transit

### What is monitoring used for?

Monitoring is used to understand whether the system is healthy and meeting expectations.

Common goals:
- performance tracking (latency, throughput, resource usage)
- confirming services are running correctly (uptime/health)
- error detection (5xx rates, failed jobs, timeouts)
- capacity planning (how close you are to CPU/RAM/network limits)
- security alerting (unexpected spikes, suspicious patterns)

### How is the monitoring tool collecting data?

Typically, monitoring data is collected using **agents** installed on servers.

Those agents:
- continuously observe system and service metrics
- ship metrics/logs to a monitoring backend
- power dashboards and reports (graphs, trends)

### How to monitor your web server QPS

To monitor **QPS (requests per second)** on your web server tier, you generally:

1. Install a monitoring agent on the web servers.
2. Configure request counting (for example, by exporting web server metrics or parsing access logs).
3. Build a dashboard chart that shows QPS over time.
4. Add alert thresholds (warning/critical) for unusual spikes or drops.
5. Review patterns regularly to understand traffic behavior and capacity needs.

## Infrastructure issues

### Why terminating SSL at the load balancer level is an issue

If SSL/TLS is terminated at the load balancer, traffic from the load balancer to backend servers may be **unencrypted**.

That means if an attacker gains access to the internal network (or can sniff internal traffic), they could capture sensitive data (credentials, personal information). Encrypting LB → backend traffic (or using mTLS) reduces this risk.

### Why having only one MySQL server capable of accepting writes is an issue

When only the **Primary** database accepts writes, it becomes a critical dependency.

If it fails:
- the application cannot save new data or updates
- the site may become read-only, or fail in any feature that requires writes

### Why having servers with all the same components might be a problem

Putting **web server + application server + database** on every server can create drawbacks:
- wastes resources (every machine runs everything, even when not needed)
- makes scaling less flexible (you can’t easily scale just the web tier or just the database tier)
- increases attack surface (more services exposed per server means more things to secure)
