# Task-001 – Install Docker Packages and Start Docker Service

## Scenario

The Nautilus DevOps team wants to start containerizing applications for testing.

To prepare the target application server, Docker packages must be installed and the Docker service must be started.

The requested configuration must be applied on App Server 3:

```text
Server: stapp03
Required packages: docker-ce and Docker Compose
Required service: docker
```

## Requirement

Install Docker CE and Docker Compose on App Server 3.

Start and enable the Docker service.

## Initial State

The `docker` command was not available on the server.

When checking Docker, the command returned:

```text
docker: command not found
```

Also, attempting to install `docker-ce` initially failed because the Docker CE repository was not available in the enabled repositories.

## Troubleshooting Path

```text
Access App Server 3
↓
Confirm correct server
↓
Check if docker command exists
↓
Attempt package installation
↓
Identify missing Docker CE repository
↓
Install repository management tools
↓
Add Docker CE repository
↓
Install docker-ce and Docker Compose plugin
↓
Start and enable docker service
↓
Validate Docker and Compose
```

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Check whether Docker is installed:

```bash
docker --version
```

Check whether Docker packages are available:

```bash
sudo dnf install -y docker-ce docker-compose
```

Initial result:

```text
No match for argument: docker-ce
No match for argument: docker-compose
```

This showed that the required Docker packages were not available from the current enabled repositories.

Search for available Docker-related packages:

```bash
sudo dnf search docker
sudo dnf list available '*docker*'
sudo dnf list available '*compose*'
```

## First Finding

```text
The docker command was missing.
The docker-ce package was not available because the Docker CE repository was missing.
```

## Incorrect Path Tested

Docker-compatible Podman packages were available:

```text
podman-docker
podman-compose
```

However, this was not the correct solution for this task.

The KodeKloud checker expected:

```text
docker-ce
docker compose
docker service
```

Podman Docker emulation was not accepted as a replacement for Docker CE.

## Fix

Install repository management tools:

```bash
sudo dnf install -y dnf-plugins-core
```

Add the official Docker CE repository:

```bash
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

Install Docker CE and Docker Compose plugin:

```bash
sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Start and enable the Docker service:

```bash
sudo systemctl enable --now docker
```

## Command Explanation

```bash
sudo dnf install -y dnf-plugins-core
```

Installs tools needed to manage DNF repositories.

```bash
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

Adds the Docker CE repository so that Docker packages become available.

```bash
sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Installs the Docker Engine, Docker CLI, container runtime, build plugin, and Docker Compose plugin.

```bash
sudo systemctl enable --now docker
```

Starts Docker immediately and enables it to start automatically after reboot.

## Validation

Verify Docker packages are installed:

```bash
rpm -qa | grep -E 'docker-ce|docker-compose-plugin'
```

Expected result should include:

```text
docker-ce
docker-ce-cli
docker-compose-plugin
```

Verify Docker version:

```bash
docker --version
```

Expected result:

```text
Docker version ...
```

Verify Docker Compose plugin:

```bash
docker compose version
```

Expected result:

```text
Docker Compose version ...
```

Verify Docker service is active:

```bash
sudo systemctl is-active docker
```

Expected result:

```text
active
```

Verify Docker can respond:

```bash
sudo docker ps
```

Expected result:

```text
CONTAINER ID   IMAGE   COMMAND   CREATED   STATUS   PORTS   NAMES
```

## Final Result

Docker CE and Docker Compose plugin were installed successfully.

The Docker service was started and enabled.

Docker commands were validated successfully on the target server.

## Lessons Learned

- `docker: command not found` means the Docker CLI is not installed or not available in the current PATH.
- If `docker-ce` is not found, the first thing to check is whether the Docker CE repository is configured.
- Podman can emulate Docker commands, but it is not always accepted when a task specifically requires Docker CE.
- Modern Docker Compose is usually provided by the `docker-compose-plugin` package and is run using `docker compose`.
- Always validate both the package installation and the service state.
- A service being installed is not enough; it must also be running for Docker commands to work correctly.

## Knowledge Check

### Question 1

When `dnf install docker-ce` returns `No match for argument: docker-ce`, what should be checked first?

A. Whether the Docker CE repository is configured  
B. Whether `/tmp` is empty  
C. Whether Apache is running  
D. Whether the hostname is uppercase  

**Answer:** A

### Question 2

Which command installs Docker CE and the Docker Compose plugin after adding the Docker CE repository?

A. `sudo dnf install -y podman-docker podman-compose`  
B. `sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`  
C. `sudo yum remove docker`  
D. `sudo systemctl restart sshd`  

**Answer:** B
