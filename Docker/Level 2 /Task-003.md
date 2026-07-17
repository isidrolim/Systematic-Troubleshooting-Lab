# Create Image From a Running Container

## Scenario

A Nautilus developer made changes inside a running Docker container and wanted to preserve those changes as a reusable Docker image.

The requested operation must be performed on:

```text
Server: stapp03
Running Container: ubuntu_latest
New Image: blog:datacenter
```

---

## Requirement

Create a new Docker image named:

```text
blog:datacenter
```

from the running container:

```text
ubuntu_latest
```

---

## Current State

The running container `ubuntu_latest` already existed.

No image named `blog:datacenter` existed locally.

---

## Desired State

A new Docker image named:

```text
blog:datacenter
```

exists locally and represents the current state of the running container.

---

## Dependency Path

```text
Access App Server 3
↓
Docker Engine running
↓
Container exists
↓
Container running
↓
Create image from container
↓
Validate new image
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

Verify local images:

```bash
docker images
```

Result:

```text
ubuntu:latest existed

blog:datacenter did not exist
```

---

## Gap Analysis

| Current State | Desired State |
|---------------|---------------|
| Running container exists | New reusable image exists |
| No backup image available | Container state preserved as image |

The running container needed to be converted into a reusable Docker image.

---

## First Finding

```text
The container already contained the required changes.

The only missing requirement was creating a new image from the container.
```

---

## Fix

Create a new image from the running container:

```bash
docker commit ubuntu_latest blog:datacenter
```

---

## Command Explanation

```bash
docker commit
```

Creates a new Docker image from the current filesystem state of a running container.

Source:

```text
ubuntu_latest
```

Destination:

```text
blog:datacenter
```

Unlike `docker tag`, `docker commit` creates a completely new image.

---

## Validation

Verify the new image exists:

```bash
docker images
```

Expected:

```text
REPOSITORY   TAG

blog         datacenter

ubuntu       latest
```

---

## Final Result

The running container was successfully converted into a reusable Docker image named:

```text
blog:datacenter
```

---

## Lessons Learned

- `docker commit` creates a new image from a running container.
- `docker commit` captures the current filesystem state of the container.
- `docker tag` creates another name for an existing image.
- `docker commit` creates a brand new image.
- Always validate image creation using `docker images`.

---

## Engineering Insight

Docker supports two different image workflows.

### Development / Lab Workflow

```text
Image

↓

Container

↓

Modify manually

↓

docker commit

↓

New Image
```

Useful for:

- Learning
- Debugging
- Experiments
- Temporary backups

---

### Production Workflow

```text
Dockerfile

↓

docker build

↓

Image

↓

Registry

↓

Deployment
```

This approach is repeatable, version-controlled, and fully automated.

---

## Production Thinking

Although `docker commit` is useful for preserving manual changes, it is rarely used in production.

Production images should be built from a Dockerfile so that every build is reproducible and version-controlled.

If a container contains important manual changes, those changes should eventually be converted into Dockerfile instructions before deployment.

---

## Knowledge Check

### Question 1

What is the primary purpose of `docker commit`?

A.

Create another tag

B.

Create a new image from a running container

C.

Delete a container

D.

Rename an image

**Answer:** B

---

### Question 2

Which workflow is preferred in production?

A.

Modify container → docker commit

B.

Dockerfile → docker build

C.

docker cp → docker commit

D.

docker tag → docker commit

**Answer:** B
