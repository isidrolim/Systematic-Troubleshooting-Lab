# Pull and Re-Tag a Docker Image

## Scenario

The Nautilus DevOps team is preparing a new containerized application for testing.

Before deployment, a specific BusyBox image must be downloaded and assigned an additional tag for future reference.

The requested operation must be performed on:

```text
Server: stapp01
Image: busybox:musl
New Tag: busybox:blog
```

---

## Requirement

Pull the Docker image:

```text
busybox:musl
```

Create a new tag:

```text
busybox:blog
```

---

## Current State

No BusyBox image existed on the Docker host.

The required image needed to be downloaded before a new tag could be created.

---

## Desired State

Both image tags should exist locally:

```text
busybox:musl

busybox:blog
```

Both tags should reference the same Docker image.

---

## Dependency Path

```text
Access App Server 1
↓
Docker Engine running
↓
Check local images
↓
Pull image if missing
↓
Create new tag
↓
Validate both image tags
```

---

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Check local Docker images:

```bash
docker images
```

Result:

```text
busybox:musl was not present.
```

---

## Gap Analysis

| Current State | Desired State |
|---------------|---------------|
| busybox:musl does not exist locally | busybox:musl exists locally |
| busybox:blog does not exist | busybox:blog points to the same image |

The image must first be downloaded before a new tag can be created.

---

## First Finding

```text
The required BusyBox image was not available on the Docker host.
```

---

## Fix

Pull the required image:

```bash
docker pull busybox:musl
```

Create a second tag:

```bash
docker tag busybox:musl busybox:blog
```

---

## Command Explanation

Pull an image from Docker Hub:

```bash
docker pull busybox:musl
```

Downloads the BusyBox image with the `musl` tag.

---

Create another tag for the same image:

```bash
docker tag busybox:musl busybox:blog
```

Creates another reference to the same image.

No additional image is downloaded.

No additional storage is consumed.

---

## Validation

List Docker images:

```bash
docker images
```

Expected:

```text
REPOSITORY   TAG    IMAGE ID

busybox      musl   <same_image_id>

busybox      blog   <same_image_id>
```

Notice both tags point to the same **IMAGE ID**.

This confirms that both tags reference the same Docker image.

---

## Lessons Learned

- `docker images` lists locally available Docker images.
- `docker pull` downloads an image from Docker Hub.
- `docker tag` creates another reference to the same image.
- Creating a new tag does **not** duplicate the image.
- Always validate image operations by checking the IMAGE ID.

---

## Engineering Insight

A Docker image can have multiple tags.

Think of a tag as a human-readable label pointing to the same image.

Example:

```text
Image ID

5a93558ae921

▲                ▲
│                │

busybox:musl     busybox:blog
```

Both tags reference exactly the same image.

Docker only removes the actual image when **no tags reference it anymore**.

This makes tagging an efficient way to version or categorize images without consuming additional storage.

---

## Production Thinking

In production environments, image tags are commonly used to manage deployment versions.

Example:

```text
myapp:1.0.0

↓

myapp:stable

↓

myapp:production
```

Multiple tags may reference the same image, allowing teams to promote images through different environments without rebuilding or downloading them again.

Understanding this concept is fundamental for CI/CD pipelines, Kubernetes deployments, and container image versioning.

---

## Knowledge Check

### Question 1

Why does creating a new Docker tag not consume additional disk space?

A. Docker compresses the image.

B. Docker stores only one image and multiple tags reference the same IMAGE ID.

C. Docker deletes the original image.

D. Docker automatically creates symbolic links.

**Answer:** B

---

### Question 2

Which command creates another tag for an existing Docker image?

A.

```bash
docker rename
```

B.

```bash
docker tag
```

C.

```bash
docker pull
```

D.

```bash
docker export
```

**Answer:** B
