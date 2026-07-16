# Allow User to Run Docker Without sudo

## Scenario

A Nautilus project developer requires permission to run Docker commands on Application Server 1.

The user account already exists on the server but cannot execute Docker commands without using `sudo`.

The requested configuration must be applied on:

```text
Server: stapp01
User: jim
Required Group: docker
```

---

## Requirement

Allow user `jim` to run Docker commands without using `sudo`.

---

## Current State

The user `jim` already existed on the server.

The Docker group also existed.

However, `jim` was not a member of the Docker group.

---

## Desired State

User `jim` is a member of the `docker` group and can execute Docker commands without using `sudo`.

---

## Dependency Path

```text
Access App Server 1
↓
Verify user exists
↓
Verify docker group exists
↓
Check group membership
↓
Add user to docker group
↓
Validate membership
```

---

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Verify the user exists:

```bash
cat /etc/passwd | grep jim
```

Verify the Docker group exists:

```bash
cat /etc/group | grep docker
```

Current result:

```text
docker:x:992:tony
```

Only `tony` was a member of the Docker group.

---

## Gap Analysis

| Current State | Desired State |
|---------------|---------------|
| Docker group exists | Docker group exists |
| User jim exists | User jim exists |
| Jim is NOT a member of docker group | Jim IS a member of docker group |

The missing requirement was group membership.

---

## First Finding

```text
User jim already existed.

Docker group already existed.

Jim was not a member of the docker group.
```

---

## Fix

Add `jim` to the Docker group:

```bash
sudo usermod -aG docker jim
```

---

## Command Explanation

```bash
usermod
```

Modifies an existing user account.

```bash
-a
```

Append the user to additional supplementary groups.

```bash
-G
```

Specify the supplementary group(s).

```bash
docker
```

The group that grants permission to access the Docker daemon.

```bash
jim
```

The existing Linux user being modified.

Using `-aG` ensures existing supplementary groups are preserved while adding the new group.

---

## Validation

Verify group membership:

```bash
id jim
```

or

```bash
groups jim
```

Expected result:

```text
docker
```

appears in the user's supplementary groups.

---

## Final Result

User `jim` was successfully added to the Docker group.

The user can now execute Docker commands without requiring `sudo` after starting a new login session.

---

## Lessons Learned

- Docker permissions are controlled through Linux group membership.
- Existing users should be modified using `usermod`.
- `-aG` appends a user to supplementary groups without removing existing memberships.
- Always verify that both the user and group already exist before making changes.
- Group membership changes usually require a new login session to take effect.

---

## Engineering Insight

Docker does not grant access based on usernames.

Instead, it relies on the Linux permission model.

```text
User
↓

Group Membership

↓

Docker Socket

↓

Docker Daemon
```

By default, the Docker daemon is owned by the `docker` group.

Adding a user to this group allows the user to communicate directly with the Docker daemon without requiring root privileges.

This demonstrates how Linux permissions and Docker integrate to control access securely.

---

## Production Thinking

In production environments, adding users to the Docker group should be carefully controlled.

Any user who belongs to the Docker group effectively has elevated access to the host because Docker can start privileged containers, mount host filesystems, and interact directly with the Docker daemon.

Before adding users to the Docker group, consider:

- Does the user really need Docker access?
- Is rootless Docker a better option?
- Can the task be delegated through CI/CD instead?

Following the principle of least privilege helps reduce security risk.

---

## Knowledge Check

### Question 1

Why is `usermod` used instead of `useradd`?

A.

`useradd` only creates new users.

B.

`usermod` modifies an existing user.

C.

`usermod` creates Docker containers.

D.

`useradd` modifies Linux groups.

**Answer:** B

---

### Question 2

Why is the `-a` option important when using:

```bash
usermod -aG docker jim
```

A.

It automatically creates the Docker group.

B.

It appends the new group without removing existing supplementary groups.

C.

It activates the Docker service.

D.

It grants root privileges.

**Answer:** B
