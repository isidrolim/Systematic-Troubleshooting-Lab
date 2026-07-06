# Delete Docker Container

## Scenario

A temporary Docker container created for testing purposes must be removed from Application Server 1 after testing has been completed.

The requested operation must be performed on:

```text
Server: stapp01
Container: kke-container
Required State: Removed
```

---

## Requirement

Delete the Docker container `kke-container` from Application Server 1.

---

## Current State

The container `kke-container` exists and is currently running.

---

## Desired State

The container `kke-container` no longer exists on the Docker host.

---

## Dependency Path

```text
Access App Server 1
↓
Docker Engine available
↓
Container exists
↓
Determine container state
↓
Stop container (if running)
↓
Remove container
↓
Validate container removal
```

---

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Verify Docker is installed:

```bash
docker --version
```

List all containers:

```bash
sudo docker ps -a
```

Current Result:

```text
Container: kke-container
Status: Up (running)
```

---

## Gap Analysis

| Current State | Desired State |
|---------------|---------------|
| Container exists and is running | Container does not exist |

The container cannot be safely removed while it is running.

---

## First Failure

```text
The container is still running.
```

---

## Fix

Stop the running container:

```bash
sudo docker stop kke-container
```

Remove the stopped container:

```bash
sudo docker rm kke-container
```

---

## Validation

Verify the container state after stopping:

```bash
sudo docker ps -a
```

Expected:

```text
Status: Exited
```

Verify the container has been removed:

```bash
sudo docker ps -a
```

Expected:

```text
No container named kke-container exists.
```

---

## Lessons Learned

- Always verify whether the container is running before attempting to remove it.
- Running containers should be stopped gracefully before deletion.
- `docker ps -a` displays both running and stopped containers.
- `docker stop` changes the container state from **Running** to **Exited**.
- `docker rm` permanently removes the container metadata.

---

## Engineering Insight

Deleting a Docker container is not simply running `docker rm`.

A container has a lifecycle:

```text
Created
↓
Running
↓
Stopped (Exited)
↓
Removed
```

A good engineer first identifies the current lifecycle stage before performing an operation.

In this task:

```text
Current State:
Running

Desired State:
Removed

Gap:
A running container cannot be removed cleanly.

Solution:
Stop the container first, then remove it.
```

Thinking in terms of **Current State → Desired State → Gap → Fix** helps avoid unnecessary errors and builds a repeatable troubleshooting methodology for production environments.

---

## Knowledge Check

### Question 1

Why was the container stopped before running `docker rm`?

A. Docker cannot remove a running container safely.

B. Docker containers must always be restarted first.

C. Docker automatically removes stopped containers.

D. The image must be deleted first.

**Answer:** A

---

### Question 2

Which command displays both running and stopped Docker containers?

A.

```bash
docker ps
```

B.

```bash
docker images
```

C.

```bash
docker ps -a
```

D.

```bash
docker inspect
```

**Answer:** C
