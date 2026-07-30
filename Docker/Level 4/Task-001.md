# Troubleshoot a Failing Dockerfile Build

## Scenario

The Nautilus DevOps team is creating a custom Apache Docker image for application deployment. During development, the Docker image build started failing because of incorrect instructions in the Dockerfile.

The objective was to identify the first failure, correct only the invalid instructions, and successfully build the image without modifying the valid base image, certificates, or application files.

---

## Requirement

- Investigate the Dockerfile located at:

```text
/opt/docker/Dockerfile
```

- Fix the build errors.
- Do **not** modify:
  - Base image
  - Valid Dockerfile configuration
  - Certificates
  - HTML content

- Successfully build the image.

---

## Initial State

Project structure:

```text
/opt/docker/
├── Dockerfile
├── certs/
│   ├── server.crt
│   └── server.key
└── html/
    └── index.html
```

Docker build failed because of incorrect Dockerfile instructions.

---

## Symptom

Docker could not successfully build the custom Apache image.

---

## Business Impact

Application deployment was blocked because the required Docker image could not be produced.

---

## Dependency Path

```text
Dockerfile
        ↓
Dockerfile Syntax
        ↓
Instructions Execute Sequentially
        ↓
Image Builds Successfully
        ↓
Image Available
        ↓
Container Deployment
```

---

## Verification Before Fix

Verify the project files:

```bash
hostname

ls -lah /opt/docker
```

Review the Dockerfile:

```bash
cat /opt/docker/Dockerfile
```

List Dockerfile instructions only:

```bash
grep -nE '^(FROM|ADD|RUN|COPY)' Dockerfile
```

---

## Troubleshooting Path

### Step 1 — Validate the Base Image

Verified:

```dockerfile
FROM httpd:2.4.43
```

The base image was valid and matched the task requirements.

No change required.

---

### Step 2 — Review Dockerfile Instructions

Observed:

```dockerfile
ADD sed -i ...
```

Dockerfile instruction purposes:

| Instruction | Purpose |
|-------------|----------|
| FROM | Base image |
| RUN | Execute Linux commands during build |
| COPY | Copy local files |
| ADD | Add files or remote URLs |

The `ADD` instruction was incorrectly being used to execute Linux commands.

---

### Step 3 — Identify the First Failure

Four Dockerfile lines began with:

```dockerfile
ADD sed
```

These should instead execute Linux commands.

Correct instruction:

```dockerfile
RUN sed
```

---

### Step 4 — Apply the Fix

Safely replace only the affected lines:

```bash
sudo sed -i.bak 's/^ADD sed/RUN sed/' /opt/docker/Dockerfile
```

A backup file was automatically created.

---

### Step 5 — Verify the Fix

```bash
grep -nE '^(FROM|ADD|RUN|COPY)' Dockerfile
```

Expected:

```text
1 FROM
4 RUN
3 COPY
0 ADD
```

Confirmed.

---

### Step 6 — Build the Image

```bash
docker build -t httpd-ssl-test .
```

Build completed successfully.

---

## First Finding

The build failure was not caused by:

- Base image
- Certificates
- HTML files

The first failure was an incorrect Dockerfile instruction.

The Dockerfile attempted to execute Linux commands using:

```dockerfile
ADD
```

instead of:

```dockerfile
RUN
```

---

## Validation

Verify the build:

```bash
docker images
```

Expected:

```text
httpd-ssl-test
```

Verify no containers are required:

```bash
docker ps
```

The image build itself satisfied the task requirements.

---

## Lessons Learned

- Dockerfile instructions each have a specific purpose.
- `RUN` executes commands during image build.
- `ADD` is used for adding files or remote URLs.
- Docker executes Dockerfile instructions sequentially.
- Always identify the first failing instruction before making changes.
- Validate Dockerfile structure before rebuilding.

---

## Engineering Insight

This task reinforced an important production troubleshooting principle:

> Fix the **first confirmed failure**, not every line that looks suspicious.

Instead of rewriting the Dockerfile, the investigation followed this process:

```text
Inspect
      ↓
Validate
      ↓
Identify First Failure
      ↓
Apply Smallest Safe Fix
      ↓
Verify
      ↓
Build
```

This minimizes operational risk and preserves valid configuration.

---

## Production Thinking

When troubleshooting Docker builds:

- Validate the Dockerfile before editing.
- Change only the confirmed failing instruction.
- Create backups before modifying configuration.
- Rebuild immediately after the first fix.
- Let the next build error reveal the next failure instead of making multiple speculative changes.
- Preserve supplied application data unless explicitly instructed otherwise.

---

## Knowledge Check

1. What is the difference between `RUN`, `COPY`, and `ADD`?
2. Why was `ADD sed ...` invalid?
3. Why was `FROM httpd:2.4.43` left unchanged?
4. Why is fixing the first confirmed failure safer than modifying multiple lines at once?
5. Why was `grep` used before rebuilding?
6. What evidence proved the Docker image built successfully?
