# Configure Crontab Access

## Skills Practiced

- Cron
- User Access Control
- cron.allow
- cron.deny
- Linux Security
- Validation

---

## Scenario

The security team required tighter control over who is allowed to create and manage cron jobs on App Server 3.

Only authorized users should be permitted to use `crontab`.

---

## Requirement

Configure cron access on **App Server 3**:

Allow:

```text
yousuf
```

Deny:

```text
ryan
```

---

## Initial State

Current verification:

```bash
hostname
```

Verify cron access files:

```bash
ls -l /etc/cron.allow
ls -l /etc/cron.deny
```

Result:

```text
cron.allow  → did not exist

cron.deny   → already existed
```

---

## Troubleshooting Path

```text
Access App Server 3
↓

Verify current server

↓

Check cron access mechanism

↓

Inspect cron.allow

↓

Inspect cron.deny

↓

Determine required access

↓

Update allow/deny lists

↓

Validate configuration
```

---

## Verification Before Fix

Verify current cron access files:

```bash
ls -l /etc/cron.allow
ls -l /etc/cron.deny
```

Verify users exist:

```bash
getent passwd yousuf
getent passwd ryan
```

---

## First Finding

```text
cron.allow did not exist.

cron.deny existed.

No explicit access control was configured for the required users.
```

---

## Fix

Create `/etc/cron.allow` containing:

```text
yousuf
```

Update `/etc/cron.deny` to include:

```text
ryan
```

One way is using a text editor:

```bash
sudo nano /etc/cron.allow
```

Contents:

```text
yousuf
```

Then edit:

```bash
sudo nano /etc/cron.deny
```

Add:

```text
ryan
```

---

## Validation

Display the files:

```bash
cat /etc/cron.allow

cat /etc/cron.deny
```

Expected:

```text
cron.allow

yousuf
```

```text
cron.deny

ryan
```

KodeKloud validation completed successfully.

---

## Lessons Learned

- `cron.allow` defines which users are permitted to use `crontab`.
- `cron.deny` lists users explicitly denied access.
- When `cron.allow` exists, only listed users are allowed to use `crontab`.
- Security configurations should follow the principle of least privilege.
- Always verify existing configuration before creating new files.

---

## Engineering Insight

This task demonstrated another important production principle:

```text
Verify

↓

Understand current configuration

↓

Modify only what is necessary

↓

Validate
```

The goal was not simply to "make the task pass."

It was to understand how Linux decides who may use the `crontab` command.

---

## Knowledge Check

1. What is the purpose of `/etc/cron.allow`?
2. What is the purpose of `/etc/cron.deny`?
3. Which file takes precedence when both exist?
4. Why is an allow-list generally considered more secure than relying only on a deny-list?
5. How would you verify that a user is allowed to use `crontab`?
