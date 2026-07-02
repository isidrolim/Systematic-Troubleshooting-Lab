# Deploy Nginx Container on Application Server

## Scenario

The Nautilus DevOps team is conducting application deployment tests on selected application servers.

They required an Nginx container deployment on Application Server 1.

The requested container must be created on App Server 1:

```text
Server: stapp01
Container name: nginx_1
Image: nginx:alpine
Required state: running
```

## Requirement

Create a Docker container named `nginx_1` using the `nginx:alpine` image.

Ensure the container is in a running state.

## Initial State

Docker was already installed and running on App Server 1.

No existing containers were running before creating the required Nginx container.

## Troubleshooting Path

```text
Access App Server 1
↓
Confirm correct server
↓
Verify Docker is installed
↓
Verify Docker service is active
↓
Check existing containers
↓
Create nginx_1 container using nginx:alpine
↓
Validate container is running
```

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Check Docker version:

```bash
docker --version
```

Check Docker service status:

```bash
sudo systemctl is-active docker
```

Check existing running containers:

```bash
sudo docker ps
```

## First Finding

```text
Docker was installed.
Docker service was active.
No running container named nginx_1 existed yet.
```

## Fix

Create and run the Nginx container in detached mode:

```bash
sudo docker run -d --name nginx_1 nginx:alpine
```

## Command Explanation

```bash
sudo docker run
```

Creates and starts a new container.

```bash
-d
```

Runs the container in detached mode, meaning it runs in the background and returns the terminal prompt immediately.

```bash
--name nginx_1
```

Assigns the container name `nginx_1`.

```bash
nginx:alpine
```

Uses the `nginx` image with the `alpine` tag.

If the image is not available locally, Docker automatically pulls it from the remote image registry.

## Validation

Check that the container is running:

```bash
sudo docker ps
```

Expected result:

```text
CONTAINER ID   IMAGE          COMMAND                  STATUS        PORTS     NAMES
<container_id> nginx:alpine   "/docker-entrypoint..."  Up ...        80/tcp    nginx_1
```

Check all containers if needed:

```bash
sudo docker ps -a
```

Expected result:

```text
nginx_1 should exist and have a running status.
```

## Final Result

The `nginx_1` container was created successfully using the `nginx:alpine` image.

The container was running successfully on App Server 1.

## Lessons Learned

- `docker run` creates and starts a new container.
- `-d` runs the container in detached/background mode.
- `--name` assigns a custom container name.
- Docker automatically pulls an image if it is not already available locally.
- `docker ps` shows currently running containers.
- A container task is not complete just because the command runs; the final container state must be validated.

## Knowledge Check

### Question 1

Why was the `-d` option used when creating the Nginx container?

A. To delete the container after it exits  
B. To run the container in the background  
C. To display Docker logs only  
D. To rename the image  

**Answer:** B

### Question 2

Which command creates a running container named `nginx_1` using the `nginx:alpine` image?

A. `docker pull nginx:alpine`  
B. `docker run -d --name nginx_1 nginx:alpine`  
C. `docker ps nginx_1`  
D. `docker start nginx:alpine`  

**Answer:** B
