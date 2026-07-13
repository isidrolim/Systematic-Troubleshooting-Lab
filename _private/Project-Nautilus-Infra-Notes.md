# Project Nautilus – Private Infrastructure Notes

> **Private Note:** This file contains lab infrastructure details and credentials.  
> Keep this repository private and do not link this file from the main `README.md`.

---

## Overview

Project Nautilus is run by the Naval subdivision within xFusionCorp Industries.

The Nautilus application helps naval forces make smart procurement decisions for manned and unmanned maritime systems while ensuring operational requirements are met.

The project aims to:

- Provide operational support
- Improve safety
- Extend the life of existing machines
- Reduce cost of ownership

---

## Current Repertoire

Project Nautilus currently supports the following areas:

1. Sonar Technology and Systems
2. LUSV - Large Unmanned Surface Vehicles
3. Autonomous Unmanned Undersea Pods
4. Nuclear Submarines
5. Laser Guidance Systems

---

## Application Architecture

Nautilus is a three-tier application deployed in the Stratos Datacenter in the North America Region.

```text
Client Tier
↓
Load Balancer
↓
Application Tier
↓
Data Tier
```

### Client Tier

The client tier is usually a web browser.

It is responsible for:

- Displaying HTML resources
- Sending HTTP requests
- Processing HTTP responses

### Load Balancer

Nginx is used as the HTTP load balancer.

Its role is to distribute incoming HTTP traffic across multiple application servers.

```text
Client
↓
Nginx Load Balancer
↓
stapp01 / stapp02 / stapp03
```

### Application Tier

The application tier uses a LAMP stack.

LAMP usually includes:

- Linux
- Apache HTTP Server
- MariaDB or MySQL
- PHP

The application servers host the Nautilus web application.

### Data Tier

The data tier stores application data.

MariaDB is used as the relational database system.

```text
Application Servers
↓
MariaDB Database Server
```

---

## Shared Services

### Storage Filer

A NAS storage filer provides reliable external storage for the application tier servers.

### SFTP Server

SFTP is used to transfer data between remote systems using SSH-based file transfer.

### Backup Server

The backup server is used as a staging backup system for short-term archival.

### Jump Server

The jump server acts as an SSH gateway into the remote network hosting the Nautilus application.

In most tasks, access usually starts from the jump host before connecting to the target server.

---

## Infrastructure Summary

```text
North America Region
└── Stratos Datacenter
    ├── Load Balancer
    ├── Application Servers
    ├── Database Server
    ├── Storage Server
    ├── Backup Server
    ├── Mail Server
    ├── Jump Host
    └── Jenkins Server
```

---

## SSH Access Quick Reference

> SSH format:
>
> ```bash
> ssh <user>@<hostname>
> ```
>
> The password is entered when prompted.

---

## Infrastructure Details

| Server Name | Hostname | User | Password | Purpose |
|---|---|---|---|---|
| Application Server 1 | `stapp01` | `tony` | `Ir0nM@n` | Hosts Nautilus Application 1 |
| Application Server 2 | `stapp02` | `steve` | `Am3ric@` | Hosts Nautilus Application 2 |
| Application Server 3 | `stapp03` | `banner` | `BigGr33n` | Hosts Nautilus Application 3 |
| Load Balancer Server | `stlb01` | `loki` | `Mischi3f` | Distributes traffic for Nautilus HTTP |
| Database Server | `stdb01` | `peter` | `Sp!dy` | Hosts Nautilus Database |
| Storage Server | `ststor01` | `natasha` | `Bl@kW` | Stores data for Nautilus Servers |
| Backup Server | `stbkp01` | `clint` | `H@wk3y3` | Manages backups for Nautilus Servers |
| Mail Server | `stmail01` | `groot` | `Gr00T123` | Manages email services for Nautilus Servers |
| Jump Host Server | `jump-host` | `thor` | `mjolnir123` | Provides secure access to the datacenter |
| Jenkins Server | `jenkins` | `jenkins` | `j@rv!s` | Runs Jenkins for CI/CD pipeline |

---

## Copy/Paste SSH Commands

### Jump Host Server

```bash
ssh -o StrictHostKeyChecking=no thor@jump-host
```

Password:

```text
mjolnir123
```

---

### Application Server 1

```bash
ssh -o StrictHostKeyChecking=no tony@stapp01
```

Password:

```text
Ir0nM@n
```

---

### Application Server 2

```bash
ssh -o StrictHostKeyChecking=no steve@stapp02
```

Password:

```text
Am3ric@
```

---

### Application Server 3

```bash
ssh -o StrictHostKeyChecking=no banner@stapp03
```

Password:

```text
BigGr33n
```

---

### Load Balancer Server

```bash
ssh -o StrictHostKeyChecking=no loki@stlb01
```

Password:

```text
Mischi3f
```

---

### Database Server

```bash
ssh -o StrictHostKeyChecking=no peter@stdb01
```

Password:

```text
Sp!dy
```

---

### Storage Server

```bash
ssh -o StrictHostKeyChecking=no natasha@ststor01
```

Password:

```text
Bl@kW
```

---

### Backup Server

```bash
ssh -o StrictHostKeyChecking=no clint@stbkp01
```

Password:

```text
H@wk3y3
```

---

### Mail Server

```bash
ssh -o StrictHostKeyChecking=no groot@stmail01
```

Password:

```text
Gr00T123
```

---

### Jenkins Server

```bash
ssh -o StrictHostKeyChecking=no jenkins@jenkins
```

Password:

```text
j@rv!s
```

---

## Service and Port Reference

| Service | Port | Notes |
|---|---:|---|
| SSH | `22/tcp` | Used for server access |
| SFTP | `22/tcp` | SSH-based file transfer |
| HTTP | `80/tcp` | Used by the Nautilus web application |
| MySQL/MariaDB | `3306/tcp` | Used by the Nautilus database |
| NFS | N/A | Used by shared storage services |
| Rsync | `22/tcp` | Used for backup or synchronization over SSH |

---

## Common Access Pattern

Typical workflow:

```text
Local Terminal
↓
SSH to Jump Host
↓
SSH to target Nautilus server
↓
Perform assigned task
↓
Validate result
```

Example:

```bash
ssh thor@jump-host
```

Then from the jump host:

```bash
ssh tony@stapp01
```

---

## Notes for KodeKloud Engineer Tasks

When solving KodeKloud Engineer tasks, always confirm:

1. Which server the task mentions
2. Which user should be used
3. Whether root or sudo access is required
4. The exact hostname
5. The validation command after the fix

Example:

```text
Task says: App Server 2
Target hostname: stapp02
User: steve
SSH command: ssh steve@stapp02
Password: Am3ric@
```

---

## Security Reminder

This file contains lab credentials.

Keep it private.

Recommended location:

```text
_private/Project-Nautilus-Infra-Notes.md
```

Do not place this information in a public repository.
