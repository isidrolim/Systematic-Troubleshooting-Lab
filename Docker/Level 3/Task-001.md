# Create a Docker Macvlan Network

## Scenario

The Nautilus DevOps team is preparing several Docker environments for future application deployments. A new Docker network must be created on **App Server 3 (stapp03)** using the **macvlan** driver with the specified subnet and IP allocation range.

---

## Requirement

Create a Docker network with the following configuration:

- Network Name: `blog`
- Driver: `macvlan`
- Subnet: `10.10.1.0/24`
- IP Range: `10.10.1.0/24`

---

## Initial State

- Docker Engine installed.
- Existing default Docker networks (`bridge`, `host`, `none`).
- No network named `blog`.

---

## Symptom

The required Docker network does not exist.

---

## Business Impact

Future containers that depend on the `blog` network cannot be deployed, preventing application connectivity and delaying environment provisioning.

---

## Dependency Path

```text
Correct Server
        ↓
Docker Engine Running
        ↓
Existing Networks Verified
        ↓
Network Driver Selected
        ↓
Subnet Defined
        ↓
IP Allocation Range Defined
        ↓
Network Created
        ↓
Configuration Verified
```

---

## Verification Before Fix

Verify the target environment:

```bash
hostname
```

Verify Docker is available:

```bash
docker ps
```

List existing Docker networks:

```bash
docker network ls
```

Inspect Docker network help to discover the required options:

```bash
docker network create --help
```

Relevant options:

```text
--driver
--subnet
--ip-range
```

---

## Systematic Elimination

### Step 1 — Verify the Correct Server

Confirmed the task is being performed on:

```text
stapp03
```

---

### Step 2 — Verify Existing Networks

```bash
docker network ls
```

Confirmed that the `blog` network does not already exist.

---

### Step 3 — Map the Ticket to Docker Options

| Ticket Requirement | Docker Option |
|-------------------|---------------|
| blog | Network Name |
| macvlan | `--driver` |
| 10.10.1.0/24 | `--subnet` |
| 10.10.1.0/24 | `--ip-range` |

---

### Step 4 — Create the Network

```bash
docker network create \
  --driver macvlan \
  --subnet 10.10.1.0/24 \
  --ip-range 10.10.1.0/24 \
  blog
```

---

## First Finding

Initially, there was uncertainty because both the subnet and IP range were identical.

Investigation showed that:

- **Subnet** defines the overall network.
- **IP Range** defines the addresses Docker may allocate.

Although it is more common for the IP range to be a subset of the subnet, Docker accepts both values being identical, and this matched the ticket requirements.

---

## Validation

Verify the network exists:

```bash
docker network ls
```

Inspect the network configuration:

```bash
docker network inspect blog
```

Expected output:

- Driver: `macvlan`
- Subnet: `10.10.1.0/24`
- IPRange: `10.10.1.0/24`

Challenge completed successfully.

---

## Lessons Learned

- Docker networking is managed separately from containers.
- `docker network create --help` is an excellent way to discover command options instead of memorizing them.
- CIDR notation (`/24`) represents the subnet mask (`255.255.255.0`).
- `--subnet` defines the network.
- `--ip-range` defines the pool of IP addresses Docker allocates.
- `docker network inspect` should always be used to validate the final configuration.

---

## Engineering Insight

The biggest takeaway from this task was understanding the difference between **implementing requirements** and **making assumptions**.

Even though using the same value for the subnet and IP range is uncommon, the ticket explicitly requested it. Rather than changing the design based on assumptions, the correct engineering approach was:

1. Question the requirement.
2. Verify Docker supports it.
3. Implement exactly what was requested.
4. Validate the resulting configuration.

---

## Production Thinking

Before creating production Docker networks:

- Verify existing network ranges to avoid IP overlap.
- Select the correct network driver for the workload.
- Confirm subnet and IP allocation strategy with the network design.
- Always validate using `docker network inspect`.

---

## Knowledge Check

1. What is the purpose of the `macvlan` network driver?
2. What is the difference between `--subnet` and `--ip-range`?
3. Why should you inspect an existing Docker network before creating a new one?
4. Why was it acceptable for the subnet and IP range to be identical in this task?
5. Which command allows you to verify the final Docker network configuration?
