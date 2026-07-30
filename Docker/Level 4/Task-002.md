# Troubleshoot a Failing Docker Compose Deployment

## Scenario

The Nautilus DevOps team attempted to deploy a Python web application and a Redis database using Docker Compose.

The deployment failed due to multiple misconfigurations in the Compose file. The objective was to identify the configuration errors one at a time, correct only the invalid settings, preserve the existing container names, and successfully deploy the application.

---

## Requirement

- Investigate:

```text
/opt/docker/docker-compose.yml
```

- Fix the Docker Compose configuration.
- Do **not** modify:
  - Existing container names
  - Valid configuration
  - Application source code

- Successfully deploy the application.

---

## Initial State

Project structure:

```text
/opt/docker/
├── docker-compose.yml
└── app/
    ├── Dockerfile
    ├── app.py
    └── requirements.txt
```

Deployment failed due to Compose configuration errors.

---

## Symptom

Docker Compose could not successfully deploy the Python application.

---

## Business Impact

The application stack consisting of:

- Python
- Redis

could not be started, preventing application testing.

---

## Dependency Path

```text
Compose File
        ↓
YAML Syntax
        ↓
Compose Schema
        ↓
Build Context
        ↓
Image Build
        ↓
Container Dependencies
        ↓
Application Startup
```

---

## Verification Before Fix

Verify the correct server:

```bash
hostname
```

Verify project files:

```bash
ls -lah /opt/docker
```

Validate the Compose file:

```bash
docker compose config
```

Rather than immediately deploying, use the validator to identify the **first failing configuration**.

---

## Troubleshooting Path

### Step 1 — Validate Compose Structure

The first validation error revealed:

```yaml
service:
```

Compose expects:

```yaml
services:
```

Corrected:

```yaml
services:
```

Validated again.

---

### Step 2 — Validate Service Definition

The next validation identified:

```yaml
from:
```

Docker Compose does not support:

```yaml
from:
```

Correct instruction:

```yaml
image:
```

Corrected only the affected line.

Validated again.

---

### Step 3 — Validate Build Context

Deployment failed with:

```text
unable to prepare context:
path "/app" not found
```

Investigation showed:

```text
/opt/docker/
└── app/
```

The build context was corrected from:

```yaml
build: /app
```

to:

```yaml
build: ./app
```

The relative path correctly referenced the project directory.

---

### Step 4 — Validate Compose Keys

Observed:

```yaml
port:
```

Compose expects:

```yaml
ports:
```

Corrected only the key.

---

### Step 5 — Validate Service Dependency

Compose reported:

```text
depends on undefined service redis
```

Investigation showed:

```yaml
redis_app:
```

while:

```yaml
depends_on:
  - redis
```

references the **Compose service name**, not the container name.

Corrected:

```yaml
redis_app:
```

to:

```yaml
redis:
```

while preserving:

```yaml
container_name: redis
```

---

### Step 6 — Validate Configuration

```bash
docker compose config
```

Validation completed successfully.

---

### Step 7 — Deploy

```bash
docker compose up
```

Containers started successfully.

---

## First Finding

Rather than one major problem, the Compose file contained multiple independent configuration issues.

The troubleshooting process corrected only the **first confirmed failure** each time:

1. Invalid top-level key
2. Invalid image definition
3. Invalid build context
4. Invalid Compose key
5. Invalid service dependency

Each correction was immediately validated before proceeding to the next.

---

## Validation

Validate the configuration:

```bash
docker compose config
```

Deploy:

```bash
docker compose up
```

Verify:

```bash
docker ps
```

Expected running containers:

```text
python
redis
```

Application logs confirmed:

```text
Running on:
http://0.0.0.0:5000
```

Challenge completed successfully.

---

## Lessons Learned

- `docker compose config` should always be the first troubleshooting tool.
- Docker Compose validates configuration before deployment.
- `services:` is the required top-level key.
- `image:` specifies existing images.
- `build:` defines the build context.
- Relative paths are resolved from the Compose file location.
- `depends_on` references **Compose service names**, not container names.
- Correcting one failure at a time greatly simplifies troubleshooting.

---

## Engineering Insight

This task demonstrated one of the most important troubleshooting principles:

> Never fix multiple suspected problems at once.

Instead:

```text
Validate
      ↓
Read Error
      ↓
Identify First Failure
      ↓
Apply Smallest Safe Fix
      ↓
Validate Again
      ↓
Repeat
```

Each new validation reveals the next dependency failure.

This minimizes operational risk and prevents introducing unnecessary changes.

---

## Production Thinking

When troubleshooting Docker Compose:

- Always begin with:

```bash
docker compose config
```

- Validate before deploying.
- Preserve known-good configuration.
- Correct only the reported failure.
- Revalidate after every change.
- Treat every validation error as evidence, not as something to guess.

---

## Knowledge Check

1. Why is `docker compose config` preferable to immediately running `docker compose up`?
2. What is the difference between a Compose service name and `container_name`?
3. Why did `depends_on` fail even though `container_name: redis` existed?
4. Why was `build: /app` incorrect?
5. Why is troubleshooting one error at a time safer than making multiple edits?
6. What evidence proved the deployment was finally successful?
