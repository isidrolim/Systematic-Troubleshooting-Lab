# Deploy an Apache Container Using Docker Compose

## Scenario

The Nautilus application development team provided static website content that must be hosted using an Apache HTTP server running inside a Docker container.

The DevOps team must define the deployment using Docker Compose so the container configuration is reproducible and can be managed as a declared desired state.

---

## Requirement

On **App Server 2 (`stapp02`)**:

- Create the Compose file at the exact path:

```text
/opt/docker/docker-compose.yml
```

- Use the `httpd:latest` image.
- Create a container named `httpd`.
- Map host port `5000` to container port `80`.
- Bind mount host directory `/opt/data` to:

```text
/usr/local/apache2/htdocs
```

- Keep the container running.
- Do not modify the existing content inside `/opt/data`.

---

## Initial State

- Docker Engine was installed.
- Docker Compose was available.
- No Docker containers or images were present.
- The directory `/opt/docker` existed.
- Static website content already existed at:

```text
/opt/data/index1.html
```

---

## Symptom

No containerized Apache service existed to host the static website.

---

## Business Impact

The application development team's static content was not available over HTTP.

Without a reproducible Compose definition, future deployment and recovery would also depend on manually constructed Docker commands.

---

## Dependency Path

```text
Correct Server
        ↓
Docker Engine Available
        ↓
Docker Compose Available
        ↓
Static Content Exists
        ↓
Compose File Created
        ↓
httpd Image Available
        ↓
Container Created
        ↓
Host Port 5000 Published
        ↓
Host Content Mounted
        ↓
Apache Running
        ↓
Website Reachable
```

---

## Verification Before Fix

Verify the correct server:

```bash
hostname
```

Expected:

```text
stapp02
```

Verify existing containers:

```bash
docker ps -a
```

Verify existing images:

```bash
docker images
```

Verify Docker Compose:

```bash
docker compose version
```

Observed:

```text
Docker Compose version v5.0.2
```

Verify the required directories:

```bash
ls -lah /opt/docker
ls -lah /opt/data
```

Verify the website content:

```bash
cat /opt/data/index1.html
```

---

## Troubleshooting Path

The task initially appeared to require a Dockerfile.

After reviewing the requirement, it became clear that no custom image needed to be built.

The existing `httpd` image only needed runtime configuration:

- Container name
- Port publishing
- Bind mount
- Running state

Therefore, the correct artifact was a **Docker Compose file**, not a Dockerfile.

---

## Systematic Elimination

### Dockerfile versus Compose

A Dockerfile describes how to build an image:

```text
Dockerfile
    ↓
docker build
    ↓
Docker image
```

A Compose file describes how containers should run:

```text
compose.yaml
    ↓
docker compose up
    ↓
Running containers
```

The task required the second workflow.

---

## First Finding

Docker Compose represents many familiar `docker run` options as YAML keys.

| Docker CLI | Docker Compose |
|---|---|
| Image argument | `image:` |
| `--name` | `container_name:` |
| `-p` / `--publish` | `ports:` |
| `-v` / `--mount` | `volumes:` |
| `docker run -d` | `docker compose up -d` |

This showed that Compose was not a completely new container model. It was a declarative representation of the same runtime configuration.

---

## Fix

Create the required file:

```bash
sudo nano /opt/docker/docker-compose.yml
```

Add:

```yaml
services:
  web:
    image: httpd:latest
    container_name: httpd
    ports:
      - "5000:80"
    volumes:
      - /opt/data:/usr/local/apache2/htdocs
```

Start the service from the Compose file directory:

```bash
cd /opt/docker
docker compose up -d
```

Alternatively, specify the file explicitly:

```bash
docker compose -f /opt/docker/docker-compose.yml up -d
```

---

## Validation

Validate the Compose syntax:

```bash
docker compose -f /opt/docker/docker-compose.yml config
```

Verify the Compose service:

```bash
docker compose -f /opt/docker/docker-compose.yml ps
```

Verify the running container:

```bash
docker ps
```

Expected evidence:

```text
Container name: httpd
Port mapping:   0.0.0.0:5000->80/tcp
Status:         Up
```

Inspect the container:

```bash
docker inspect httpd
```

Confirm:

```text
Source:      /opt/data
Destination: /usr/local/apache2/htdocs
```

Perform an application-level test:

```bash
curl http://localhost:5000/index1.html
```

Expected website content:

```text
Welcome to xFusionCorp Industries.
```

The KodeKloud checker confirmed successful completion.

---

## Lessons Learned

- Dockerfiles and Docker Compose files solve different problems.
- A Dockerfile builds an image.
- Docker Compose declares how containers should run.
- `services:` contains the workloads managed by Compose.
- `image:` identifies the Docker image.
- `container_name:` sets the actual container name.
- `ports:` uses `host:container` order.
- `volumes:` can use `host_path:container_path` syntax.
- YAML indentation is part of the configuration syntax.
- `docker compose config` should be used before deployment to validate YAML and the resolved configuration.

---

## Engineering Insight

Docker Compose converts an imperative command such as:

```bash
docker run \
  -d \
  --name httpd \
  -p 5000:80 \
  -v /opt/data:/usr/local/apache2/htdocs \
  httpd:latest
```

into a declarative desired state:

```yaml
services:
  web:
    image: httpd:latest
    container_name: httpd
    ports:
      - "5000:80"
    volumes:
      - /opt/data:/usr/local/apache2/htdocs
```

The Compose file is easier to:

- review
- version-control
- reproduce
- audit
- modify
- redeploy

This is an early introduction to Infrastructure as Code principles.

---

## Production Thinking

Before deploying a Compose workload in production:

- Validate the file with `docker compose config`.
- Avoid using floating tags such as `latest` where reproducibility matters.
- Confirm that the host port is not already occupied.
- Verify host directory ownership and permissions.
- Consider mounting static content as read-only:

```yaml
volumes:
  - /opt/data:/usr/local/apache2/htdocs:ro
```

- Add restart policies where appropriate.
- Add health checks instead of assuming that a running container means a healthy application.
- Store the Compose file in version control.
- Back up persistent host data independently of the container.

---

## Knowledge Check

1. What is the difference between a Dockerfile and a Docker Compose file?
2. What is the difference between a Compose service name and `container_name`?
3. Why is the port mapping written as `5000:80`?
4. What happens to `/usr/local/apache2/htdocs` when `/opt/data` is mounted over it?
5. Which command validates a Compose file without deploying it?
6. Why is an HTTP test stronger evidence than only checking `docker ps`?
7. Why might a read-only bind mount be safer for static website content?
