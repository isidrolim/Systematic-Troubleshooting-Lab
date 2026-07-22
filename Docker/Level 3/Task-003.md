# Deploy an Nginx Alpine Container with Port Publishing

## Scenario

The Nautilus DevOps team is preparing to migrate applications into Docker-based environments. As part of the migration, a lightweight Nginx container must be deployed using the `nginx:alpine` image and exposed through a specific host port for testing.

---

## Requirement

On **Application Server 3 (stapp03)**:

- Pull the `nginx:alpine` image.
- Create a container named `demo`.
- Map host port **5004** to container port **80**.
- Keep the container running.

---

## Initial State

- Docker Engine installed and running.
- No container named `demo`.
- Required image not yet confirmed.

---

## Symptom

There is no running Nginx container exposing port **5004** on the host.

---

## Business Impact

The application cannot be accessed from outside the container because the required port mapping has not been configured.

---

## Dependency Path

```text
Correct Server
        ↓
Docker Engine Running
        ↓
Image Available
        ↓
Container Created
        ↓
Port Published
        ↓
Container Running
        ↓
Configuration Verified
        ↓
Application Reachable
```

---

## Verification Before Fix

Verify the correct server:

```bash
hostname
```

Verify Docker:

```bash
docker ps
```

Verify the image:

```bash
docker images
```

Pull the required image if necessary:

```bash
docker pull nginx:alpine
```

---

## Systematic Elimination

### Step 1 — Verify the Image

Confirm that `nginx:alpine` exists locally.

```bash
docker images
```

---

### Step 2 — Discover Docker Run Options

Instead of memorizing commands:

```bash
docker run --help
```

Relevant options discovered:

```text
-d, --detach
--name
-p, --publish
```

---

### Step 3 — Understand Port Mapping

Docker publishes ports using the format:

```text
HostPort:ContainerPort
```

For this task:

```text
Host
5004
   │
   ▼
Container
80
```

Result:

```text
5004:80
```

---

### Step 4 — Create the Container

```bash
docker run \
  -d \
  --name demo \
  -p 5004:80 \
  nginx:alpine
```

---

## First Finding

Initially, there was uncertainty about the order of the published ports.

The key realization was that Docker consistently follows:

```text
Host
   ↓
Container
```

Therefore:

```text
5004:80
```

means:

- Host listens on **5004**
- Traffic is forwarded to container port **80**

---

## Validation

Verify the running container:

```bash
docker ps
```

Expected:

```text
0.0.0.0:5004->80/tcp
```

Inspect the container configuration:

```bash
docker inspect demo
```

Confirm:

- Container name: `demo`
- Image: `nginx:alpine`
- PortBindings:
  - HostPort: `5004`
  - ContainerPort: `80`

(Optional) Verify the application:

```bash
curl http://localhost:5004
```

Challenge completed successfully.

---

## Lessons Learned

- `docker run --help` is an effective way to discover options.
- `-p` is the short form of `--publish`.
- Docker publishes ports using the format:

```text
HostPort:ContainerPort
```

- `docker ps` verifies the running state.
- `docker inspect` verifies the complete container configuration.

---

## Engineering Insight

This task introduced Docker's port publishing model.

Without publishing a port, a containerized application remains isolated inside Docker's internal network.

Publishing creates a controlled bridge between the host network and the container.

```text
Host
Port 5004
     │
     ▼
Docker
     │
     ▼
Container
Port 80
```

This same networking concept is later used in:

- Docker Compose
- Kubernetes Services
- Ingress Controllers
- Load Balancers

Understanding port publishing is fundamental to exposing containerized applications.

---

## Production Thinking

Before exposing production containers:

- Verify required ports are not already in use.
- Use non-privileged host ports when appropriate.
- Validate published ports with `docker ps`.
- Confirm configuration using `docker inspect`.
- Perform an application-level test (`curl` or browser) after deployment.

---

## Knowledge Check

1. What is the difference between a container port and a host port?
2. Why does Docker use the format `HostPort:ContainerPort`?
3. What is the purpose of the `-p` (`--publish`) option?
4. Which command verifies that the port mapping is correctly configured?
5. Why is `docker inspect` a better validation tool than relying only on `docker ps`?
6. What would happen if you started the container without `-p 5004:80`?
