# Transfer a Docker Image Between Two Docker Hosts

## Scenario

The Nautilus DevOps team created a custom Docker image named `news:datacenter` on **App Server 1 (stapp01)**.

Another team needs to use the exact same image on **App Server 3 (stapp03)** for testing. Since no container registry is available, the image must be exported as an archive, securely transferred, and loaded into Docker on the destination server.

---

## Requirement

- Save the Docker image `news:datacenter` into an archive on **App Server 1**.
- Transfer the archive to **App Server 3**.
- Load the image into Docker on **App Server 3**.
- Preserve the same repository name and tag.

---

## Initial State

### App Server 1

- Docker installed.
- Image `news:datacenter` exists.

### App Server 3

- Docker installed.
- No Docker images available.

---

## Symptom

The required Docker image only exists on **App Server 1**.

---

## Business Impact

The testing team cannot deploy containers because the required image is unavailable on the target server.

---

## Dependency Path

```text
App Server 1
Docker Image
news:datacenter
        │
        ▼
docker save
        │
        ▼
Linux TAR Archive
        │
        ▼
Secure File Transfer
(rsync)
        │
        ▼
App Server 3
Linux TAR Archive
        │
        ▼
docker load
        │
        ▼
Docker Image
news:datacenter
```

---

## Verification Before Fix

### App Server 1

Verify hostname:

```bash
hostname
```

Verify Docker:

```bash
docker ps
```

Verify image:

```bash
docker images
```

---

### App Server 3

Verify hostname:

```bash
hostname
```

Verify Docker:

```bash
docker ps
```

Verify that no images exist:

```bash
docker images
```

---

## Systematic Elimination

### Step 1 — Verify the Source Image

Confirm that the required image exists.

```bash
docker images
```

---

### Step 2 — Discover the Correct Docker Command

Initially, `docker export` was considered.

After reviewing Docker help:

```bash
docker export --help
```

it became clear that:

```text
docker export
```

exports a **container filesystem**, not a Docker image.

The correct command is:

```text
docker save
```

which preserves:

- Image layers
- Repository
- Tag
- Metadata

---

### Step 3 — Save the Image

```bash
docker save \
  -o /tmp/news-datacenter.tar \
  news:datacenter
```

---

### Step 4 — Validate the Archive

```bash
ls -lah /tmp/news-datacenter.tar
```

Confirm that the archive exists and has a reasonable file size.

---

### Step 5 — Transfer the Archive

Install `rsync` if necessary.

Transfer the archive:

```bash
rsync -av /tmp/news-datacenter.tar banner@stapp03:/tmp/
```

---

### Step 6 — Verify the Archive on App Server 3

```bash
ls -lah /tmp/news-datacenter.tar
```

---

### Step 7 — Discover the Correct Import Command

Review Docker help:

```bash
docker load --help
```

Relevant option:

```text
-i
```

Read image archive from a file.

---

### Step 8 — Load the Image

```bash
docker load \
  -i /tmp/news-datacenter.tar
```

---

## First Finding

The first assumption was:

```text
docker export
```

However, investigation showed:

| Command | Purpose |
|----------|----------|
| docker export | Export a container filesystem |
| docker save | Save Docker image with repository, tag and layers |

Since the ticket required preserving the image name and tag, `docker save` and `docker load` were the correct tools.

---

## Validation

Verify the image exists on App Server 3:

```bash
docker images
```

Expected output:

```text
REPOSITORY    TAG
news          datacenter
```

Challenge completed successfully.

---

## Lessons Learned

- `docker export` works with containers.
- `docker save` works with images.
- `docker load` restores Docker images.
- Docker images can be transported as ordinary Linux files.
- `rsync` is an efficient way to transfer Docker archives between servers.
- Always verify artifacts before and after transfer.

---

## Engineering Insight

One of the most valuable concepts from this task is understanding that a Docker image can temporarily leave Docker.

```text
Docker Image
        │
        ▼
Linux File (.tar)
        │
        ▼
Network Transfer
        │
        ▼
Linux File (.tar)
        │
        ▼
Docker Image
```

Docker itself is not responsible for moving images between hosts.

Instead, the image becomes a normal Linux file that can be:

- copied
- moved
- archived
- backed up
- transferred with rsync or scp

This concept forms the foundation of container image distribution.

---

## Production Thinking

In production environments, images are usually **not** transferred manually.

Instead:

```text
Developer
      │
      ▼
Docker Build
      │
      ▼
Container Registry
(Harbor, ECR, GCR, Docker Hub)
      │
      ▼
Production Servers
docker pull
```

However, understanding `docker save` and `docker load` is extremely valuable when:

- working in air-gapped environments
- performing disaster recovery
- migrating isolated servers
- creating offline backups
- troubleshooting image corruption

---

## Knowledge Check

1. What is the difference between `docker save` and `docker export`?
2. Why is `docker load` used instead of `docker import`?
3. What information does `docker save` preserve?
4. Why did we verify the TAR archive before transferring it?
5. Why is `rsync` commonly used instead of `cp` when transferring files between servers?
6. How are Docker images normally distributed in production environments?
