# Exercise-003 – Production Docker Compose with Network and Named Volume

## Scenario

Create a production-style Docker deployment using a custom bridge network, a named volume for logs, and a custom-built company image.

---

## Requirement

Deploy:

- Image: `company-web:2.0`
- Container: `production-web`
- Host Port: `8080`
- Container Port: `80`
- Custom bridge network
- Subnet:

```text
172.30.50.0/24
```

- Named volume:

```text
nginx-logs
```

---

## Dependency Path

```text
Dockerfile
        ↓
Image
        ↓
Compose
        ↓
Bridge Network
        ↓
Named Volume
        ↓
Running Container
        ↓
Application
```

---

## Compose File

```yaml
networks:
  app-network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.30.50.0/24

volumes:
  nginx-logs:

services:
  web:
    build: .
    image: company-web:2.0
    container_name: production-web

    ports:
      - "8080:80"

    networks:
      - app-network

    volumes:
      - nginx-logs:/var/log/nginx

    restart: unless-stopped
```

---

## Validation

```bash
docker compose build
docker compose up -d
docker ps
docker network inspect app-network
docker volume inspect nginx-logs
curl http://localhost:8080
```

---

## Lessons Learned

- Docker Compose can manage multiple infrastructure components.
- Custom bridge networks isolate workloads.
- Named volumes persist data independently of containers.
- Images should be built before deployment.

---

## Engineering Insight

This deployment resembles a simplified production environment.

The same concepts appear later in Kubernetes:

| Docker Compose | Kubernetes |
|----------------|------------|
| Service | Pod |
| Network | CNI |
| Named Volume | Persistent Volume |
| Dockerfile | Container Image |
| Restart Policy | Pod Restart Policy |

Understanding Docker Compose provides an excellent bridge toward Kubernetes.

---

## Knowledge Check

1. Why use a named volume instead of a bind mount for logs?
2. Why define a custom subnet?
3. What happens if the container is removed?
4. Which resources survive container deletion?
5. How would this architecture evolve in Kubernetes?
