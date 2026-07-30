# Deploy a Multi-Service PHP and MariaDB Stack Using Docker Compose

## Scenario

The Nautilus Application Development team completed development of a PHP application and requested a complete containerized deployment before production rollout.

The deployment required a Docker Compose stack containing:

- PHP Apache Web Server
- MariaDB Database Server

The objective was to create the Compose file, correctly configure both services, and successfully deploy the application stack.

---

## Requirement

Create:

```text
/opt/finance/docker-compose.yml
```

The stack must deploy:

### Web Service

- Image: `php` (Apache variant)
- Container name: `php_blog`
- Host Port: `5001`
- Container Port: `80`
- Bind mount:

```text
Host:
/var/www/html

↓

Container:
/var/www/html
```

---

### Database Service

- Image:

```text
mariadb:latest
```

- Container name:

```text
mysql_blog
```

- Host Port:

```text
3306
```

- Container Port:

```text
3306
```

- Bind mount:

```text
Host:
/var/lib/mysql

↓

Container:
/var/lib/mysql
```

Create:

- Database:

```text
database_blog
```

- Custom database user (not root)
- Strong user password
- Strong root password

---

## Initial State

Existing directories:

```text
/opt/finance
```

```text
/var/www/html
```

Application source:

```text
index.php
```

Compose file did not yet exist.

---

## Symptom

The PHP application stack could not be deployed.

---

## Business Impact

Developers could not validate the application because neither the web service nor the database service was running.

---

## Dependency Path

```text
Compose File
        ↓
Compose Syntax
        ↓
Web Service
        ↓
Database Service
        ↓
Environment Variables
        ↓
Volumes
        ↓
Port Publishing
        ↓
Containers Running
        ↓
PHP ↔ MariaDB Connectivity
        ↓
Application Available
```

---

## Verification Before Fix

Verify server:

```bash
hostname
```

Verify project directory:

```bash
ls -lah /opt/finance
```

Verify web content:

```bash
ls -lah /var/www/html
```

Verify Compose syntax:

```bash
docker compose config
```

---

## Troubleshooting Path

### Step 1 — Build the Compose Structure

Create:

```yaml
services:
```

Define two services:

```text
web

db
```

---

### Step 2 — Configure the Web Service

```yaml
image: php:apache
container_name: php_blog
ports:
  - "5001:80"

volumes:
  - /var/www/html:/var/www/html
```

---

### Step 3 — Configure the Database Service

```yaml
image: mariadb:latest

container_name: mysql_blog

ports:
  - "3306:3306"

volumes:
  - /var/lib/mysql:/var/lib/mysql
```

---

### Step 4 — Configure MariaDB Initialization

```yaml
environment:
  MYSQL_DATABASE: database_blog
  MYSQL_USER: bloguser
  MYSQL_PASSWORD: P@$$w0rd!@
  MYSQL_ROOT_PASSWORD: P@$$w0rd!@
```

A non-root application user was created as required.

---

### Step 5 — Validate Compose

```bash
docker compose config
```

Validation completed successfully.

---

### Step 6 — Deploy

```bash
docker compose up -d
```

---

## First Finding

During troubleshooting, several important Docker Compose concepts became clear.

### Compose Service Name

```yaml
services:

  db:
```

This becomes the internal DNS hostname.

Containers communicate using:

```text
db
```

—not:

```text
localhost
```

because each container has its own network namespace.

---

### Container Name

```yaml
container_name:
```

Only changes the Docker container name.

It is **not** used by:

```yaml
depends_on:
```

or Docker's internal DNS.

---

### Environment Variables

MariaDB requires initialization variables.

Without:

```yaml
MYSQL_DATABASE
MYSQL_USER
MYSQL_PASSWORD
MYSQL_ROOT_PASSWORD
```

the database would not initialize correctly.

---

## Validation

Validate configuration:

```bash
docker compose config
```

Deploy:

```bash
docker compose up -d
```

Verify:

```bash
docker compose ps
```

Inspect logs:

```bash
docker compose logs db

docker compose logs web
```

Application test:

```bash
curl http://localhost:5001
```

Expected:

Application successfully served by the PHP Apache container.

Challenge completed successfully.

---

## Lessons Learned

- Docker Compose manages multiple containers as one application stack.
- Compose service names become Docker DNS hostnames.
- `container_name` does not affect service discovery.
- `docker compose config` should always be used before deployment.
- MariaDB initialization depends on environment variables.
- Bind mounts expose host storage directly to containers.
- PHP should communicate with MariaDB using:

```text
db
```

instead of:

```text
localhost
```

---

## Engineering Insight

One of the biggest architectural lessons from this exercise was understanding Docker's internal networking.

```text
Host
        │
        ▼
Docker Network
──────────────────────────────

php_blog
        │
        ▼
DNS Lookup

db
        │
        ▼
mysql_blog
```

Containers communicate using the **Compose service name**, not the container name and not localhost.

This same service-discovery model later appears in Kubernetes:

```text
Compose Service

↓

Kubernetes Service

↓

Cluster DNS
```

---

## Production Thinking

Before deploying multi-container applications:

- Validate Compose syntax.
- Use explicit image versions rather than `latest` when reproducibility matters.
- Store database credentials in environment files or secrets instead of embedding them in Compose.
- Keep databases on persistent storage.
- Verify service health using application-level tests, not only container status.
- Separate application configuration from source code.

---

## Knowledge Check

1. What is the difference between a Compose service name and `container_name`?
2. Why should PHP connect to MariaDB using `db` instead of `localhost`?
3. What is the purpose of `MYSQL_ROOT_PASSWORD`?
4. Why are `MYSQL_USER` and `MYSQL_PASSWORD` both required?
5. Why is `docker compose config` safer than immediately running `docker compose up`?
6. Why are bind mounts useful for databases?
7. Which Docker networking concept learned here directly maps to Kubernetes Services?
