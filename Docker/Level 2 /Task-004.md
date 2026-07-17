# Configure Apache Inside a Running Container

## Scenario

A Nautilus DevOps engineer was configuring an Apache web service inside a running Ubuntu container but was unable to complete the work.

The remaining configuration must be completed inside the running container.

The requested operation must be performed on:

```text
Server: stapp03
Container: kkloud
Web Server: Apache2
Required Port: 8085
```

---

## Requirement

Inside the running container:

- Install Apache2 using APT.
- Configure Apache to listen on port **8085**.
- Ensure Apache is running.
- Keep the container running after configuration.
- Verify the website is accessible on port **8085**.

---

## Current State

The container `kkloud` was already running.

Apache was not installed.

Apache configuration was still using the default HTTP port **80**.

---

## Desired State

Apache is installed.

Apache listens on port **8085**.

Apache service is running.

The website responds successfully on:

```text
http://localhost:8085
```

inside the container.

---

## Dependency Path

```text
Access App Server 3
↓

Container running

↓

Enter container

↓

Apache installed

↓

Apache listens on 8085

↓

Apache configuration valid

↓

Apache service running

↓

Website accessible
```

---

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Verify the running container:

```bash
docker ps
```

Enter the container:

```bash
docker exec -it kkloud bash
```

Check whether Apache is installed:

```bash
dpkg -l apache2
```

Current result:

```text
Apache package not installed.
```

---

## Gap Analysis

| Current State | Desired State |
|---------------|---------------|
| Apache not installed | Apache installed |
| Listening on port 80 | Listening on port 8085 |
| Service unavailable | Apache running |
| Website inaccessible | Website responds on port 8085 |

---

## First Finding

```text
Apache was not installed.

After installation, Apache configuration still used the default port 80.
```

---

## Fix

Install Apache:

```bash
apt update
apt install apache2 -y
```

Update Apache listen port:

```text
/etc/apache2/ports.conf

Listen 80

↓

Listen 8085
```

Update the VirtualHost:

```text
/etc/apache2/sites-available/000-default.conf

<VirtualHost *:80>

↓

<VirtualHost *:8085>
```

Validate Apache configuration:

```bash
apache2ctl configtest
```

Expected:

```text
Syntax OK
```

Restart Apache:

```bash
apache2ctl restart
```

---

## Command Explanation

Enter a running container:

```bash
docker exec -it kkloud bash
```

Installs Apache:

```bash
apt install apache2 -y
```

Checks Apache configuration:

```bash
apache2ctl configtest
```

Restarts Apache:

```bash
apache2ctl restart
```

---

## Validation

Verify Apache processes:

```bash
ps aux | grep '[a]pache2'
```

Verify HTTP response:

```bash
curl -I http://localhost:8085
```

Expected:

```text
HTTP/1.1 200 OK
```

The container should remain running:

```bash
docker ps
```

---

## Final Result

Apache2 was successfully installed inside the running container.

Apache was reconfigured to listen on port **8085**.

The configuration passed syntax validation.

Apache started successfully.

The website returned **HTTP 200 OK**.

---

## Lessons Learned

- Containers can be administered like normal Linux systems using `docker exec`.
- Docker troubleshooting often includes Linux troubleshooting inside the container.
- Apache configuration requires both:
  - `ports.conf`
  - VirtualHost configuration
- Always validate configuration before restarting services.
- `apache2ctl configtest` should be used before restarting Apache.
- Verify the application using HTTP, not just process status.

---

## Engineering Insight

This challenge demonstrates an important distinction between:

```text
Container Administration

and

Application Administration
```

Docker is only responsible for running the container.

Everything inside the container (Apache, Nginx, MySQL, PHP, etc.) is still administered using normal Linux commands.

The troubleshooting process therefore became:

```text
Docker

↓

Running Container

↓

Linux Operating System

↓

Apache Configuration

↓

Application Validation
```

This layered approach is commonly used when troubleshooting containerized applications in production.

---

## Production Thinking

In production environments, engineers generally avoid manually installing software inside running containers.

Instead:

```text
Dockerfile

↓

apt install apache2

↓

docker build

↓

Docker Image

↓

Deployment
```

This makes the image reproducible and version-controlled.

For lab environments and troubleshooting exercises, modifying the running container is acceptable because the objective is to understand how the application behaves inside the container.

---

## Real-World Usage

This task demonstrates why DevOps engineers need strong Linux administration skills in addition to Docker knowledge.

Although the application runs inside a container, troubleshooting still required:

- Editing Apache configuration files.
- Validating Apache syntax.
- Restarting the web server.
- Testing HTTP connectivity.

Understanding both Docker and Linux is essential when diagnosing application issues inside containers.

---

## Knowledge Check

### Question 1

Why were both `ports.conf` and `000-default.conf` modified?

A.

Only `ports.conf` controls Apache.

B.

Apache requires both the listening port and the VirtualHost configuration to match.

C.

Docker requires two configuration files.

D.

Ubuntu automatically duplicates Apache configuration.

**Answer:** B

---

### Question 2

Why was `apache2ctl configtest` executed before restarting Apache?

A.

To install Apache.

B.

To verify configuration syntax before restarting the service.

C.

To expose port 8085.

D.

To rebuild the Docker image.

**Answer:** B
