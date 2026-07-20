# Create an Nginx Container with a Bind Mount

## Scenario

The Nautilus DevOps team is testing application containerization before migrating services into Docker-based environments.

A new Nginx container must be created using the official image while mounting a host directory into the container so that files on the host become immediately available inside the container.

---

## Requirement

On **App Server 3 (stapp03)**:

- Pull the `nginx` image (preferably `latest`).
- Create a container named `official`.
- Mount host directory `/opt/itadmin` to container directory `/tmp`.
- Copy `/tmp/sample.txt` (host) into `/opt/itadmin`.
- Keep the container in the running state.

---

## Initial State

- Docker Engine installed.
- Nginx image not yet available.
- `/opt/itadmin` exists.
- `/tmp/sample.txt` exists on the host.

---

## Symptom

No running container satisfies the required configuration.

---

## Business Impact

The application testing environment cannot be deployed because the required container and shared storage are missing.

Without the bind mount, the application would not have access to the required host files.

---

## Dependency Path

```text
Correct Server
        ↓
Docker Engine Running
        ↓
Nginx Image Available
        ↓
Host Directory Exists
        ↓
sample.txt Copied
        ↓
Container Created
        ↓
Bind Mount Configured
        ↓
Container Running
        ↓
Configuration Verified
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

Verify the required directories:

```bash
ls -ld /opt/itadmin
```

Verify the source file:

```bash
ls -l /tmp/sample.txt
```

---

## Systematic Elimination

### Step 1 — Pull the Required Image

```bash
docker pull nginx
```

Confirm:

```bash
docker images
```

---

### Step 2 — Prepare the Host Directory

Copy the required file:

```bash
cp /tmp/sample.txt /opt/itadmin/
```

This ensures the file exists before the bind mount is created.

---

### Step 3 — Discover the Required Docker Options

Instead of memorizing commands:

```bash
docker run --help
```

Identify the required options:

```text
--detach
--name
--mount
```

---

### Step 4 — Create the Container

```bash
docker run \
  --detach \
  --name official \
  --mount type=bind,source=/opt/itadmin,target=/tmp \
  nginx
```

---

## First Finding

Initially there was uncertainty about bind mounts.

The important realization was:

```text
Host Directory
        │
        ▼
Container Directory
```

Docker does **not** copy the files.

The container directly accesses the host directory through the bind mount.

---

## Validation

Verify the container:

```bash
docker ps
```

Verify the bind mount:

```bash
docker inspect official
```

Expected mount:

```text
Source:
/opt/itadmin

Destination:
/tmp
```

Verify from inside the container:

```bash
docker exec official ls /tmp
```

Expected:

```text
sample.txt
```

Challenge completed successfully.

---

## Lessons Learned

- `docker run --help` is a powerful learning tool.
- `--mount` is more explicit and readable than `-v`.
- Bind mounts do **not** copy files.
- Host files become immediately available inside the container.
- The image name is the final argument of `docker run`.

---

## Engineering Insight

One of the biggest conceptual breakthroughs from this task was understanding the relationship between the host filesystem and the container filesystem.

```text
Host
/opt/itadmin
        │
        │ Bind Mount
        ▼
Container
/tmp
```

Both paths reference the **same underlying files**.

Any modification on the host is immediately visible inside the container and vice versa.

This same storage concept appears later in:

- Docker Compose
- Kubernetes Persistent Volumes (PV)
- Persistent Volume Claims (PVC)
- ConfigMaps
- Secrets

Understanding bind mounts provides the foundation for learning container storage in orchestration platforms.

---

## Production Thinking

Before deploying production containers:

- Verify the host directory exists.
- Ensure correct file permissions.
- Prefer `--mount` for readability and maintainability.
- Validate mounts using `docker inspect`.
- Confirm containers remain healthy after startup.

---

## Knowledge Check

1. What is the difference between `--mount` and `-v`?
2. What is a bind mount?
3. Why was `sample.txt` copied before creating the container?
4. Does Docker copy files when using a bind mount?
5. Which command verifies the mount configuration after the container starts?
6. Why is the image name always the last argument in `docker run`?
