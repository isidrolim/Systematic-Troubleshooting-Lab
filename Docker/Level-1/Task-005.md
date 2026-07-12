# Troubleshoot Docker Container Issue

## Scenario

The Nautilus DevOps team identified an issue with a static website running inside a Docker container on Application Server 1.

The container must serve the website correctly using a bind-mounted host directory.

The requested validation must be performed on:

```text
Server: stapp01
Container: nautilus
Host Directory: /var/www/html
Container Directory: /usr/local/apache2/htdocs
Host Port: 8085
```

---

## Requirement

Verify that:

- The container volume is correctly mapped.
- The website is accessible using:

```bash
curl http://localhost:8085
```

---

## Current State

The Docker container existed but was stopped.

The website was not initially accessible because the container was not running.

---

## Desired State

The container is running.

The bind mount is correctly configured.

The website is accessible on:

```text
http://localhost:8085
```

---

## Dependency Path

```text
Access App Server 1
↓
Docker Engine running
↓
Container exists
↓
Container running
↓
Volume correctly mounted
↓
Website responds on port 8085
↓
Validation successful
```

---

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Verify the container status:

```bash
sudo docker ps -a
```

Result:

```text
Container exists
Status: Exited
```

---

## Gap Analysis

| Current State | Desired State |
|---------------|---------------|
| Container is stopped | Container is running |
| Website unavailable | Website responds on port 8085 |

The first issue preventing success was that the container was not running.

---

## First Failure

```text
The Docker container was stopped.
```

---

## Fix

Start the container:

```bash
sudo docker start nautilus
```

---

## Continue Verification

Verify the container is running:

```bash
sudo docker ps
```

Expected:

```text
Status: Up
```

Verify the website:

```bash
curl http://localhost:8085
```

Expected:

```text
Welcome to xFusionCorp Industries!
```

---

## Investigating the Volume Mapping

Instead of assuming the container was configured correctly, inspect its configuration.

Inspect the container:

```bash
docker inspect nautilus
```

Locate the bind mounts:

```json
"HostConfig": {
    "Binds": [
        "/var/www/html:/usr/local/apache2/htdocs/"
    ]
}
```

Interpretation:

```text
Host
/var/www/html

↓

Container
/usr/local/apache2/htdocs/
```

The bind mount matched the task requirement.

---

## Validation

Verify the website:

```bash
curl http://localhost:8085
```

Verify the running container:

```bash
sudo docker ps
```

Verify the bind mount:

```bash
docker inspect nautilus
```

Expected findings:

```text
Container Status: Running

Volume Mapping:
/var/www/html
↓

/usr/local/apache2/htdocs/

Website:
Welcome to xFusionCorp Industries!
```

---

## Lessons Learned

- Always verify whether a container is running before troubleshooting application issues.
- `docker ps -a` shows both running and stopped containers.
- `docker start` changes a container from **Exited** to **Running**.
- `docker inspect` provides the complete configuration of a container.
- Bind mounts connect a host directory to a directory inside a container.
- Validate application functionality after confirming container health.

---

## Engineering Insight

This challenge demonstrates an important production troubleshooting principle:

**Do not assume configuration. Verify it.**

Instead of immediately recreating the container or changing Docker settings, the investigation followed a systematic approach:

```text
Current State
↓

Container stopped

↓

Desired State

Container running and website accessible

↓

Gap

Container not running

↓

Fix

Start container

↓

Continue Investigation

Verify bind mount

↓

Validate application
```

`docker inspect` is one of the most valuable troubleshooting commands because it reveals the container's complete runtime configuration, including:

- Bind mounts
- Port mappings
- Environment variables
- Networks
- Restart policies
- Image information

In production environments, engineers use `docker inspect` to verify how a container was deployed before making configuration changes.

---

## Knowledge Check

### Question 1

Which Docker command displays the complete runtime configuration of a container?

A.

```bash
docker logs
```

B.

```bash
docker exec
```

C.

```bash
docker inspect
```

D.

```bash
docker ps
```

**Answer:** C

---

### Question 2

Why is a bind mount used?

A. To create another Docker image

B. To allow the host and container to share the same files

C. To restart Docker automatically

D. To expose a network port

**Answer:** B
