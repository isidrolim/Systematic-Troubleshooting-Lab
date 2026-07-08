# Copy File to Docker Container

## Scenario

The Nautilus DevOps team stores confidential encrypted files on the Docker host.

A running Ubuntu container requires access to one of these encrypted files without modifying its contents.

The requested operation must be performed on Application Server 1.

```text
Server: stapp01
Container: ubuntu_latest
Source File: /tmp/nautilus.txt.gpg
Destination: /home/
```

---

## Requirement

Copy the file:

```text
/tmp/nautilus.txt.gpg
```

from the Docker host into the running container:

```text
ubuntu_latest
```

under:

```text
/home/
```

The file must remain unchanged during the copy.

---

## Current State

The encrypted file existed on the Docker host.

The Ubuntu container was already running.

The destination directory inside the container existed, but the file had not yet been copied.

---

## Desired State

The encrypted file exists inside:

```text
ubuntu_latest:/home/
```

without modifying the original file on the Docker host.

---

## Dependency Path

```text
Access App Server 1
↓
Docker Engine running
↓
Container exists and is running
↓
Source file exists on host
↓
Destination directory exists inside container
↓
Copy file
↓
Validate inside container
```

---

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Verify the container is running:

```bash
docker ps
```

Verify the source file exists:

```bash
ls -lah /tmp
```

Verify the destination directory inside the container:

```bash
docker exec ubuntu_latest ls -lah /home/
```

---

## Gap Analysis

| Current State | Desired State |
|---------------|---------------|
| File exists only on the Docker host | File exists inside the running container |

Docker provides a command specifically designed to copy files between the host and containers.

---

## First Finding

```text
The container was running.

The source file existed.

The destination directory existed.

Only the file transfer was missing.
```

---

## Fix

Copy the file from the host into the running container:

```bash
docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/home/
```

---

## Command Explanation

```bash
docker cp
```

Copies files between:

```text
Host ↔ Container
```

Source:

```text
/tmp/nautilus.txt.gpg
```

Destination:

```text
ubuntu_latest:/home/
```

Docker interprets:

```text
container:path
```

as a location **inside the running container**.

---

## Validation

Verify the file now exists inside the container:

```bash
docker exec ubuntu_latest ls -lah /home/
```

Expected result:

```text
nautilus.txt.gpg
```

The copied file should now appear inside:

```text
/home/
```

while remaining unchanged on the Docker host.

---

## Lessons Learned

- `docker cp` copies files between the Docker host and a container.
- The source can be either the host or the container.
- The destination can also be either the host or the container.
- `container:path` refers to a filesystem path inside the container.
- Always validate the copy operation by checking inside the container.

---

## Engineering Insight

This task demonstrates the difference between interacting with the **host filesystem** and the **container filesystem**.

The Docker host and a container each have their own filesystem.

Instead of opening an interactive shell inside the container, Docker provides dedicated commands for common administrative tasks.

```text
docker cp
```

is used for transferring files.

```text
docker exec
```

is used for executing Linux commands inside a running container.

A systematic approach for this task was:

```text
Current State
↓

Host has the file
Container does not

↓

Desired State

Container contains the file

↓

Gap

File has not been transferred

↓

Fix

docker cp

↓

Validation

docker exec ... ls
```

Thinking in terms of **Current State → Desired State → Gap → Validation** makes Docker troubleshooting much easier as environments become more complex.

---

## Knowledge Check

### Question 1

Which Docker command copies files between the Docker host and a running container?

A.

```bash
docker exec
```

B.

```bash
docker cp
```

C.

```bash
docker pull
```

D.

```bash
docker attach
```

**Answer:** B

---

### Question 2

Which Docker command is used to execute a Linux command inside a running container?

A.

```bash
docker cp
```

B.

```bash
docker exec
```

C.

```bash
docker ps
```

D.

```bash
docker logs
```

**Answer:** B
