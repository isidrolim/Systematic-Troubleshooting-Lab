# Dockerize and Deploy a Node.js Application

## Scenario

The Nautilus Application Development team completed a Node.js web application and requested that it be containerized and deployed for testing.

The application already contained:

```text
package.json
server.js
```

The objective was to create a proper Dockerfile, build the application image, deploy a container, and verify the application through an HTTP request.

---

## Requirement

Inside:

```text
/node_app
```

Create a **Dockerfile** that:

- Uses a Node.js base image.
- Installs dependencies using `package.json`.
- Starts the application using `server.js`.
- Exposes port **3002**.

Build the image as:

```text
nautilus/node-web-app
```

Run a container:

```text
nodeapp_nautilus
```

Publish:

```text
Host Port:      8095
Container Port: 3002
```

Verify using:

```bash
curl http://localhost:8095
```

---

## Initial State

Existing files:

```text
/node_app/
├── package.json
└── server.js
```

No Dockerfile.

No image.

No running container.

---

## Symptom

The Node.js application could not be containerized or deployed.

---

## Business Impact

Developers could not validate or demonstrate the application because it had not been packaged into a Docker image.

---

## Dependency Path

```text
Node Base Image
        ↓
Working Directory
        ↓
Copy package.json
        ↓
Install Dependencies
        ↓
Copy server.js
        ↓
Expose Application Port
        ↓
Define Startup Command
        ↓
Build Image
        ↓
Run Container
        ↓
Application Available
```

---

## Verification Before Fix

Verify project files:

```bash
cd /node_app

ls -lah
```

Inspect application dependencies:

```bash
cat package.json
```

Verify application port:

```bash
grep -nE 'listen|3002|localhost|0.0.0.0' server.js
```

Observed:

```text
PORT = 3002

HOST = 0.0.0.0
```

---

## Troubleshooting Path

### Step 1 — Select the Correct Base Image

Initial assumption:

```dockerfile
FROM alpine
```

Investigation showed the application required:

- node
- npm

Corrected:

```dockerfile
FROM node:20
```

---

### Step 2 — Define the Working Directory

```dockerfile
WORKDIR /node_app
```

Purpose:

Ensure all subsequent instructions execute inside:

```text
/node_app
```

---

### Step 3 — Copy Dependency Definition

```dockerfile
COPY package.json ./
```

---

### Step 4 — Install Dependencies

```dockerfile
RUN npm install
```

`npm` reads:

```text
package.json
```

and installs:

```text
express
```

into:

```text
node_modules
```

---

### Step 5 — Copy Application Source

```dockerfile
COPY server.js ./
```

---

### Step 6 — Document Application Port

```dockerfile
EXPOSE 3002
```

Important:

`EXPOSE` documents the intended application port.

It does **not** publish the port to the host.

---

### Step 7 — Define the Startup Command

Rather than:

```dockerfile
CMD server.js
```

use the JSON-array form:

```dockerfile
CMD ["node","server.js"]
```

This starts Node.js directly as PID 1 inside the container.

---

## Final Dockerfile

```dockerfile
FROM node:20

WORKDIR /node_app

COPY package.json ./

RUN npm install

COPY server.js ./

EXPOSE 3002

CMD ["node","server.js"]
```

---

## Build

```bash
docker build -t nautilus/node-web-app .
```

---

## Deployment

```bash
docker run \
  -d \
  --name nodeapp_nautilus \
  -p 8095:3002 \
  nautilus/node-web-app
```

---

## Validation

Verify image:

```bash
docker images
```

Verify container:

```bash
docker ps
```

Inspect logs:

```bash
docker logs nodeapp_nautilus
```

Application test:

```bash
curl http://localhost:8095
```

Expected:

```text
Welcome to xFusionCorp Industries!
```

Challenge completed successfully.

---

## Lessons Learned

- Dockerfile instructions execute sequentially.
- `WORKDIR` controls the current working directory during build.
- `COPY` moves files into the image.
- `RUN npm install` installs dependencies from `package.json`.
- `EXPOSE` documents the application port.
- `CMD` starts the application when the container launches.
- JSON-array `CMD` is preferred over shell form.

---

## Engineering Insight

This exercise demonstrated the complete lifecycle of packaging an application.

```text
Application Source
        ↓
Dockerfile
        ↓
Docker Image
        ↓
Container
        ↓
Published Port
        ↓
Application
```

Unlike previous Docker exercises that focused on containers, this task focused on **building a deployable artifact**.

This same workflow later becomes:

```text
Git Commit
      ↓
CI Pipeline
      ↓
Docker Build
      ↓
Container Registry
      ↓
Kubernetes Deployment
```

---

## Production Thinking

Before building production images:

- Use explicit image versions instead of floating tags.
- Copy only required files.
- Minimize image size.
- Keep dependencies inside the image.
- Use JSON-array `CMD`.
- Validate the application with `curl`, not only `docker ps`.
- Review image layers with:

```bash
docker history
```

---

## ⭐ Repeat Exercise

Repeat this lab in **2–3 weeks** without using notes.

Success criteria:

- Recreate the Dockerfile from memory.
- Explain **why** every instruction exists.
- Build the image.
- Deploy the container.
- Validate the application.

If you can explain every instruction without looking anything up, you've moved from memorization to understanding.

---

## Knowledge Check

1. Why was `node:20` chosen instead of `alpine`?
2. Why is `WORKDIR` required?
3. Why is `package.json` copied before `server.js`?
4. What does `npm install` actually read?
5. Why is `EXPOSE` different from `-p`?
6. Why is the JSON-array form of `CMD` preferred?
7. Why did the application listen on `0.0.0.0` instead of `localhost`?
