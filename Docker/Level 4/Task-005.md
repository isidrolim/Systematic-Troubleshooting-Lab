# Dockerize and Deploy a Python Application

## Scenario

The Nautilus Application Development team completed a Python web application and requested that it be containerized and deployed for testing.

The application already contained:

```text
/python_app/src/
├── requirements.txt
└── server.py
```

The objective was to create a proper Dockerfile, build the application image, deploy a container, and verify the application through an HTTP request.

---

## Requirement

Inside:

```text
/python_app
```

Create a **Dockerfile** that:

- Uses any valid Python base image.
- Installs dependencies from `requirements.txt`.
- Runs `server.py`.
- Exposes port **8082**.

Build the image as:

```text
nautilus/python-app
```

Run a container:

```text
pythonapp_nautilus
```

Publish:

```text
Host Port:      8097
Container Port: 8082
```

Validate using:

```bash
curl http://localhost:8097
```

---

## Initial State

Project structure:

```text
/python_app
├── Dockerfile
└── src
    ├── requirements.txt
    └── server.py
```

No image existed.

No running container.

---

## Symptom

The Python application could not be packaged into a Docker image.

---

## Business Impact

The application could not be deployed or tested.

Without a container image, developers could not validate their application.

---

## Dependency Path

```text
Python Base Image
        ↓
Working Directory
        ↓
Copy requirements.txt
        ↓
Install Python Dependencies
        ↓
Copy server.py
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
cd /python_app

ls -lah
```

Inspect dependency file:

```bash
cat src/requirements.txt
```

Inspect application:

```bash
grep -nE 'listen|8082|localhost|0.0.0.0' src/server.py
```

Confirm:

- Application listens on:

```text
0.0.0.0
```

- Port:

```text
8082
```

---

## Troubleshooting Path

### Step 1 — Select the Correct Base Image

Initial assumption:

```dockerfile
FROM python:20
```

Investigation showed that Docker Hub provides valid Python versions such as:

```dockerfile
FROM python:3.13
```

---

### Step 2 — Define the Working Directory

```dockerfile
WORKDIR /python_app
```

Purpose:

Every following instruction executes inside:

```text
/python_app
```

---

### Step 3 — Copy Dependency Definition

```dockerfile
COPY src/requirements.txt ./
```

This copies:

```text
Host

src/requirements.txt

↓

Image

/python_app/requirements.txt
```

---

### Step 4 — Install Dependencies

```dockerfile
RUN pip install -r requirements.txt
```

Purpose:

`pip` reads:

```text
requirements.txt
```

and installs all required Python packages.

---

### Step 5 — Copy Application Source

```dockerfile
COPY src/server.py ./
```

Now both:

```text
requirements.txt

server.py
```

exist inside the image.

---

### Step 6 — Document the Application Port

```dockerfile
EXPOSE 8082
```

Important:

`EXPOSE` documents the intended application port.

It does **not** publish the port to the host.

---

### Step 7 — Define the Startup Command

```dockerfile
CMD ["python","server.py"]
```

The JSON-array form starts Python directly as PID 1 inside the container.

---

## Final Dockerfile

```dockerfile
FROM python:3.13

WORKDIR /python_app

COPY src/requirements.txt ./

RUN pip install -r requirements.txt

COPY src/server.py ./

EXPOSE 8082

CMD ["python","server.py"]
```

---

## Build

```bash
docker build -t nautilus/python-app .
```

---

## Deployment

```bash
docker run \
  -d \
  --name pythonapp_nautilus \
  -p 8097:8082 \
  nautilus/python-app
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
docker logs pythonapp_nautilus
```

Application test:

```bash
curl http://localhost:8097
```

Expected:

```text
Welcome to xFusionCorp Industries!
```

Challenge completed successfully.

---

## Lessons Learned

- Python applications follow the same Docker lifecycle as Node.js applications.
- `requirements.txt` is the dependency manifest.
- `pip install -r requirements.txt` installs application dependencies.
- `WORKDIR` defines the execution context.
- `COPY` moves application files into the image.
- `EXPOSE` documents the application port.
- `CMD` starts the application.
- Docker build context determines where `COPY` looks for files.

---

## Engineering Insight

One of the biggest lessons from this exercise was recognizing that every language ecosystem follows the same containerization pattern.

```text
Application Source
        ↓
Dependency Manifest
        ↓
Install Dependencies
        ↓
Copy Application
        ↓
Expose Port
        ↓
Start Application
```

Examples:

```text
Node.js
package.json
↓

npm install
```

```text
Python
requirements.txt
↓

pip install
```

Although the package manager changes, the engineering workflow remains the same.

---

## Production Thinking

Before building production Python images:

- Pin Python versions instead of using floating tags.
- Copy dependency files before application code to maximize Docker cache usage.
- Keep images small by installing only required dependencies.
- Run application processes in the foreground.
- Validate with application-level tests instead of relying only on `docker ps`.

---

## ⭐ Repeat Exercise

Repeat this exercise in **2–3 weeks** without using notes.

Success criteria:

- Recreate the Dockerfile.
- Explain why every instruction exists.
- Build the image.
- Deploy the container.
- Validate the application.

---

## Knowledge Check

1. Why is `WORKDIR` important before running `pip install`?
2. Why is `requirements.txt` copied before `server.py`?
3. Why is `pip install -r requirements.txt` different from `pip install requirements.txt`?
4. Why does the application need to listen on `0.0.0.0`?
5. Why is `EXPOSE` different from `-p`?
6. Why is the JSON-array form of `CMD` preferred?
7. Why does Docker build context affect the `COPY` instruction?
