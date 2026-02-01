
# 3. Scale Up (Task 3)

This design focuses on scaling and resilience by removing remaining single points of failure and separating responsibilities into dedicated tiers (web, application, database).

## High-level diagram (ASCII)

```text
User / Browser
	|
	| HTTPS
	v
Load Balancer #1 (HAProxy)  <----->  Load Balancer #2 (HAProxy)
	|                                   |
	+--------------- (VIP/Cluster) -----+
					|
					v
		   Web Servers (HTTP + static)
			  |            |
			  v            v
		App Servers (logic + API)
			  |            |
			  v            v
	 Database Servers (Primary + Replica)
```

## Additional elements added (and why)

### 1) Second load balancer (HAProxy #2)

Adding a second load balancer removes the “front door” single point of failure.

Why it’s added:
- if Load Balancer #1 fails, Load Balancer #2 can take over (failover)
- enables maintenance on a load balancer without taking the site offline
- improves availability at the entry point

In practice, this is usually done via a virtual IP (VIP) and a failover mechanism (clustering).

### 2) Dedicated web server tier

Web servers specialize in:
- handling HTTP requests
- serving static content efficiently (HTML/CSS/JS/images)
- forwarding dynamic requests to the application tier

Why it’s added:
- better performance by tuning for web serving workloads
- easier horizontal scaling (add more web servers without touching the app tier)
- simpler operations (web config changes stay in the web tier)

### 3) Dedicated application server tier

Application servers specialize in:
- executing the business logic
- handling API/dynamic requests
- communicating with the database

Why it’s added:
- scale compute independently (add app servers when CPU is the bottleneck)
- clearer separation of concerns for troubleshooting and deployments
- reduces the “everything runs everywhere” complexity

### 4) Separated database servers

Database servers focus on:
- data storage
- query execution
- durability and consistency controls

Why it’s added:
- optimize hardware and configuration specifically for database workloads
- isolate the most sensitive component (easier to apply strict access controls)
- scale and tune the database layer independently

## Infrastructure benefits

### Component separation advantages

- **Better performance**: each tier can be tuned for its specific job
- **Easier scaling**: add only the tier that needs capacity (web vs app vs database)
- **Simpler management**: smaller, more focused configs per server role
- **Better security**: fewer services per host reduces the attack surface
- **Focused updates**: update one tier without forcing changes in the others

### High availability improvements

- **Load balancer clustering**: removes a single entry-point failure
- **Automatic failover**: traffic can continue flowing when a component fails
- **Maintenance-friendly**: upgrades can be performed with reduced downtime risk

### Scalability improvements

- **Horizontal scaling**: add more servers of the tier you need
- **Independent scaling**: scale the web tier separately from the database tier
- **Resource optimization**: match hardware to workload (CPU-heavy app tier vs IO-heavy DB tier)
