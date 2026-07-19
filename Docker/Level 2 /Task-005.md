# Create an Apache Docker Image Using a Dockerfile

## Scenario

The Nautilus DevOps team wants to standardize Apache deployments by creating a reusable Docker image instead of manually configuring containers.

The image must be built from Ubuntu 24.04, install Apache, reconfigure it to listen on port **3000**, and automatically start Apache whenever a container is launched.

---

## Requirement

Create a Dockerfile that:

- Uses `ubuntu:24.04` as the base image.
- Installs Apache.
- Configures Apache to listen on port **3000**.
- Exposes port **3000**.
- Starts Apache automatically when the container starts.

---

## Initial State

- Docker installed and running.
- Ubuntu 24.04 image available (or downloadable).
- No existing Dockerfile.

---

## Symptom

No reusable Docker image exists.

Apache must be installed and configured every time a new container is created.

---

## Business Impact

- Manual deployments are slow.
- Configuration becomes inconsistent.
- Difficult to reproduce environments.
- Increased operational risk.

---

## Dependency Path

```text
Dockerfile
        ↓
Base Image
        ↓
Install Apache
        ↓
Modify Apache Configuration
        ↓
Expose Application Port
        ↓
Define Startup Command
        ↓
Build Image
        ↓
Run Container
        ↓
Apache Available on Port 3000
```

---

## Verification Before Fix

Verify:

- Which Ubuntu version should be used?
- Which web server should be installed?
- Which Apache configuration files control the listening port?
- How does Docker keep a container running?

---

## Systematic Elimination

### Step 1 — Select the Base Image

Start from Ubuntu 24.04.

```dockerfile
FROM ubuntu:24.04
```

---

### Step 2 — Install Apache

Instead of manually installing inside a container, install it during image build.

```dockerfile
RUN apt update && apt install -y apache2
```

---

### Step 3 — Configure Apache

Modify Apache configuration so it listens on port 3000.

Update:

```
/etc/apache2/ports.conf
```

and

```
/etc/apache2/sites-available/000-default.conf
```

using `sed`.

---

### Step 4 — Document the Listening Port

```dockerfile
EXPOSE 3000
```

Important:

`EXPOSE` **does not change Apache's configuration.**

It documents which port the application is expected to use.

---

### Step 5 — Define the Startup Command

A Docker container remains alive only while its main process is running.

Instead of daemonizing Apache:

```bash
apache2ctl start
```

run Apache in the foreground:

```dockerfile
CMD ["apache2ctl","-D","FOREGROUND"]
```

---

## Final Dockerfile

```dockerfile
FROM ubuntu:24.04

RUN apt update && apt install -y apache2

RUN sed -i 's/Listen 80/Listen 3000/' /etc/apache2/ports.conf

RUN sed -i 's/<VirtualHost \*:80>/<VirtualHost *:3000>/' \
    /etc/apache2/sites-available/000-default.conf

EXPOSE 3000

CMD ["apache2ctl","-D","FOREGROUND"]
```

---

## Validation

Build the image.

```bash
docker build -t apache3000 .
```

Run the container.

```bash
docker run -d -p 3000:3000 apache3000
```

Verify:

```bash
docker ps
```

```bash
curl http://localhost:3000
```

Apache should return the default web page.

---

## Lessons Learned

- Dockerfile is a **recipe**, not a command.
- `FROM` defines the base image.
- `RUN` executes commands while building the image.
- `EXPOSE` documents the application port.
- `CMD` defines what runs when the container starts.
- Containers stop when their main process exits.
- Apache must run in the foreground inside a container.

---

## Engineering Insight

This lab demonstrated that Docker is an extension of Linux administration rather than a completely new technology.

Most Dockerfile instructions are simply Linux administration tasks expressed as code:

- Install packages
- Edit configuration files
- Configure services
- Define the startup process

Understanding Linux greatly simplifies learning Docker.

---

## Production Thinking

For production images:

- Combine related `RUN` commands to reduce image layers.
- Clean the APT cache to reduce image size.
- Avoid unnecessary packages.
- Pin image versions instead of using `latest`.
- Prefer explicit configuration changes over broad text replacements.

---

## Knowledge Check

1. What is the difference between `RUN` and `CMD`?
2. Why doesn't `EXPOSE` change Apache's listening port?
3. Why must Apache run in the foreground inside a container?
4. Why is `docker commit` not used when building production images?
5. What Dockerfile instruction always appears first?
