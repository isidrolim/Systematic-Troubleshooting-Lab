# Exercise-002 – Build a Custom Rocky Linux Nginx Image

## Scenario

The organization requires a company-standard Nginx image built from Rocky Linux instead of using the official Nginx image.

---

## Requirement

Build an image named:

```text
rocky-nginx:1.0
```

Run it with Docker Compose using:

- Container: `rocky-web`
- Host Port: `8090`
- Container Port: `80`

---

## Initial State

Empty project containing:

```text
Dockerfile
docker-compose.yml
site/
```

---

## Dependency Path

```text
Dockerfile
        ↓
docker build
        ↓
Image
        ↓
Compose
        ↓
Container
        ↓
Website
```

---

## Dockerfile

```dockerfile
FROM rockylinux:9

RUN dnf install -y nginx && dnf clean all

COPY site/ /usr/share/nginx/html/

EXPOSE 80

CMD ["nginx","-g","daemon off;"]
```

---

## Compose File

```yaml
services:
  web:
    build: .
    image: rocky-nginx:1.0
    container_name: rocky-web
    ports:
      - "8090:80"
```

---

## Validation

```bash
docker compose build
docker images
docker compose up -d
docker ps
docker history rocky-nginx:1.0
curl http://localhost:8090
```

Expected:

```text
Docker Configuration Lab 2
```

---

## Lessons Learned

- Dockerfile builds images.
- Compose deploys containers.
- `COPY` moves files into the image.
- `CMD` defines the foreground process.
- Docker layers are visible with `docker history`.

---

## Engineering Insight

Docker images are immutable artifacts.

Instead of manually configuring servers, configuration becomes part of the image itself.

---

## Knowledge Check

1. Why must Nginx run with `daemon off;`?
2. Why is `COPY` used instead of a bind mount?
3. What does `docker history` show?
