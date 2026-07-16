# Manage ACLs on a File

## Skills Practiced

- Linux Permissions
- Access Control Lists (ACL)
- chmod
- setfacl
- getfacl
- Validation

---

## Scenario

Following a security audit, the production support team discovered incorrect permissions on the `/etc/hostname` file on Nautilus App Server 2.

The security team requested additional Access Control List (ACL) entries for specific users while maintaining the existing file ownership.

The requested configuration must be applied on:

```text
Server : App Server 2
File   : /etc/hostname
```

---

## Requirement

Configure the file with the following requirements:

1. Owner must be `root`
2. Group owner must be `root`
3. Others should have **read-only**
4. User `ravi` must have **no permissions**
5. User `ryan` must have **read-only** permission

---

## Initial State

The file already existed.

Current ownership:

```text
Owner : root
Group : root
```

Current Linux permissions:

```text
-rw-r--r--
```

The file had no ACL entries for `ravi` or `ryan`.

---

## Troubleshooting Path

```text
Access App Server 2
↓

Confirm correct server

↓

Verify file exists

↓

Verify owner and group

↓

Inspect current ACL

↓

Determine required ACL entries

↓

Apply ACL changes

↓

Validate ACL configuration
```

---

## Verification Before Fix

Confirm current server:

```bash
hostname
```

Verify file ownership and permissions:

```bash
ls -l /etc/hostname
```

Inspect current ACL:

```bash
getfacl /etc/hostname
```

---

## First Finding

```text
Owner and group were already root.

Others already had read-only permission.

User ravi had no ACL entry.

User ryan had no ACL entry.
```

Only the user-specific ACL entries required modification.

---

## Fix

Grant no permissions to user `ravi`:

```bash
sudo setfacl -m u:ravi:--- /etc/hostname
```

Grant read-only permission to user `ryan`:

```bash
sudo setfacl -m u:ryan:r-- /etc/hostname
```

---

## Command Explanation

Grant no permissions:

```bash
sudo setfacl -m u:ravi:--- /etc/hostname
```

Grant read-only:

```bash
sudo setfacl -m u:ryan:r-- /etc/hostname
```

Linux Grammar:

```text
setfacl

↓

Modify ACL
(-m)

↓

Who?
(u = user)

↓

Target User
(ravi / ryan)

↓

Permissions
(--- / r--)

↓

Target File
(/etc/hostname)
```

---

## Validation

Display the ACL:

```bash
getfacl /etc/hostname
```

Expected result:

```text
user:ravi:---
user:ryan:r--
```

Verify ownership remains unchanged:

```bash
ls -l /etc/hostname
```

Expected:

```text
root root
```

---

## Lessons Learned

- Traditional Linux permissions only manage **Owner**, **Group**, and **Others**.
- ACLs provide permissions for specific users or groups without changing the file owner or primary group.
- `getfacl` displays the complete ACL configuration.
- `setfacl -m` modifies or adds ACL entries.
- ACLs extend normal Linux permissions instead of replacing them.
- Always inspect existing permissions before adding ACL entries.

---

## Knowledge Check

### Question 1

Which command displays the current ACL configuration of a file?

A. `ls -l`

B. `chmod`

C. `getfacl`

D. `setfacl`

**Answer:** C

---

### Question 2

Which command grants user `ryan` read-only access to `/etc/hostname`?

A.

```bash
chmod r-- /etc/hostname
```

B.

```bash
setfacl -m u:ryan:r-- /etc/hostname
```

C.

```bash
setfacl -m g:ryan:r-- /etc/hostname
```

D.

```bash
chmod o+r /etc/hostname
```

**Answer:** B
