# Task-016 – Firewall Configuration

## Skills Practiced

- firewalld
- firewall-cmd
- TCP Ports
- Firewall Zones
- systemd Services
- Persistent Firewall Rules
- Validation

---

## Scenario

The Nautilus system administration team deployed a web UI for a backup utility on Application Server 3.

The application listens on TCP port `5002`, but the host firewall must be configured to allow incoming connections to the application.

The required firewall zone is `public`.

---

## Requirement

Configure App Server 3 with the following:

```text
Server  : App Server 3
Service : firewalld
Zone    : public
Port    : 5002/tcp
```

Requirements:

1. Install `firewalld`.
2. Enable and start the `firewalld` service.
3. Allow incoming connections on `5002/tcp`.
4. Configure the rule in the `public` zone.
5. Ensure the firewall rule persists.

---

## Initial State

Confirm the correct server:

```bash
hostname
```

Check whether `firewalld` is installed:

```bash
rpm -q firewalld
```

Check the current service state:

```bash
sudo systemctl status firewalld
```

---

## Troubleshooting Path

```text
Access App Server 3
↓
Confirm correct server
↓
Check whether firewalld is installed
↓
Check whether firewalld is running
↓
Verify active firewall zone
↓
Allow 5002/tcp in public zone
↓
Reload firewall configuration
↓
Validate port and zone
```

---

## Verification Before Fix

Check the package:

```bash
rpm -q firewalld
```

Check the service:

```bash
sudo systemctl status firewalld
```

Check active zones:

```bash
sudo firewall-cmd --get-active-zones
```

Check the current configuration of the public zone:

```bash
sudo firewall-cmd --zone=public --list-all
```

---

## First Finding

The server required `firewalld` to be installed, enabled, and configured to permit incoming TCP traffic on port `5002` through the `public` zone.

---

## Fix

Install `firewalld` if it is not already installed:

```bash
sudo dnf install -y firewalld
```

Enable the service at boot and start it immediately:

```bash
sudo systemctl enable --now firewalld
```

Verify that the required interface is associated with the `public` zone:

```bash
sudo firewall-cmd --get-active-zones
```

Add TCP port `5002` permanently to the `public` zone:

```bash
sudo firewall-cmd --zone=public --permanent --add-port=5002/tcp
```

Reload the firewall configuration:

```bash
sudo firewall-cmd --reload
```

---

## Validation

Confirm that `firewalld` is running:

```bash
sudo systemctl is-active firewalld
```

Expected:

```text
active
```

Confirm it is enabled at boot:

```bash
sudo systemctl is-enabled firewalld
```

Expected:

```text
enabled
```

Verify the public zone:

```bash
sudo firewall-cmd --zone=public --list-all
```

Verify port `5002/tcp`:

```bash
sudo firewall-cmd --zone=public --query-port=5002/tcp
```

Expected:

```text
yes
```

Alternatively:

```bash
sudo firewall-cmd --zone=public --list-ports
```

Expected to include:

```text
5002/tcp
```

---

## Lessons Learned

- `firewalld` provides host-based firewall management on Linux.
- `firewall-cmd` is the command-line interface used to manage `firewalld`.
- Firewall rules belong to zones such as `public`.
- A port must include its protocol, such as `5002/tcp`.
- `--permanent` saves the rule persistently.
- `firewall-cmd --reload` loads permanent configuration into the active runtime configuration.
- `systemctl enable --now` both enables a service at boot and starts it immediately.
- Always verify the active firewall zone before adding rules.

---

## Engineering Insight

Opening a firewall port is only one layer of application connectivity.

A useful dependency path is:

```text
Client
↓
Network
↓
Host Firewall
↓
TCP Port 5002
↓
Listening Application
↓
Application Response
```

Allowing `5002/tcp` through `firewalld` does not prove that an application is actually listening on port `5002`.

In a production incident, firewall validation would therefore be followed by checks such as:

```bash
ss -tulpn | grep :5002
```

and an application-level connectivity test.

The firewall rule should also be limited to the required port and protocol rather than disabling the firewall entirely.

---

## Knowledge Check

### Question 1

What does `--permanent` do when adding a firewalld rule?

A. Immediately restarts firewalld  
B. Saves the rule so it persists  
C. Opens every TCP port  
D. Changes the default zone  

**Answer:** B

### Question 2

Which command verifies whether `5002/tcp` is allowed in the public zone?

A.

```bash
sudo firewall-cmd --zone=public --query-port=5002/tcp
```

B.

```bash
sudo systemctl status 5002
```

C.

```bash
sudo firewall-cmd --port=5002
```

D.

```bash
sudo ss --allow 5002
```

**Answer:** A
