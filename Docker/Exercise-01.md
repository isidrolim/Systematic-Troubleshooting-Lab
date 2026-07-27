# Exercise-001 – Deploy an Nginx Website Using Docker Compose

## Scenario

A development team has provided static website files that must be hosted using an Nginx container. The deployment must be reproducible using Docker Compose rather than manual `docker run` commands.

---

## Requirement

Create a Docker Compose project that:

- Uses `nginx:alpine`
- Creates a container named `lab-nginx`
- Publishes host port **8088** to container port **80**
- Mounts the host directory `/opt/lab1/site` into `/usr/share/nginx/html`
- Mounts the content as **read-only**
- Automatically restarts unless manually stopped

---

## Initial State

- Docker Engine installed
- Docker Compose installed
- Website files stored under:

```text
/opt/lab1/site
```

---

## Symptom

No web server is available to host the static content.

---

## Business Impact

Developers cannot validate or share the static website.

---

## Dependency Path

```text
Docker Compose
        ↓
Nginx Image
        ↓
Container
        ↓
Port Publishing
        ↓
Bind Mount
        ↓
Website Available
```

---

## Verification Before Fix

```bash
docker compose version
docker compose config
```

Verify website files:

```bash
ls -lah /opt/lab1/site
```

---

## Compose File

```yaml
services:
  frontend:
    image: nginx:alpine
    container_name: lab-nginx
    ports:
      - "8088:80"
    volumes:
      - /opt/lab1/site:/usr/share/nginx/html:ro
    restart: unless-stopped
```

---

## Validation

```bash
docker compose config
docker compose up -d
docker ps
docker inspect lab-nginx
curl http://localhost:8088
```

Expected output:

```text
Docker Consolidation Lab 1
```

---

## Lessons Learned

- Docker Compose is declarative.
- `ports:` represents `docker run -p`.
- `volumes:` represents `docker run --mount`.
- `:ro` creates a read-only bind mount.
- `docker compose config` validates Compose syntax before deployment.

---

## Engineering Insight

Docker Compose is effectively a YAML representation of `docker run`.

Instead of remembering a long command, the deployment becomes version-controlled Infrastructure as Code.

---

## Knowledge Check

1. Why is the mount configured as read-only?
2. What is the difference between the service name and `container_name`?
3. Why should `docker compose config` be executed before `up`?
