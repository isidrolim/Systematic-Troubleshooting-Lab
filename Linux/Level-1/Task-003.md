# Task-003 – Create User with Non-Interactive Shell

## Scenario

The system admin team at xFusionCorp Industries needed a dedicated user account for a backup agent tool.

Because this account is intended for a service or tool, it should not be used for interactive login.

The requested account must be created on App Server 1:

```text
Username: ammar
Shell: /sbin/nologin
Server: stapp01
```

## Requirement

Create a user named `ammar` on App Server 1 with a non-interactive shell.

## Initial State

The user `ammar` was not present on App Server 1.

## Troubleshooting Path

```text
Access App Server 1
↓
Confirm correct server
↓
Check if user ammar already exists
↓
Confirm non-interactive shell path
↓
Create user with /sbin/nologin shell
↓
Validate final user entry
```

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Check whether the user already exists:

```bash
id ammar
```

or:

```bash
getent passwd ammar
```

Check whether the non-interactive shell exists:

```bash
ls -l /sbin/nologin
```

## First Finding

```text
User ammar did not exist on App Server 1.
The non-interactive shell /sbin/nologin was available.
```

## Fix

Create the user with `/sbin/nologin` as the login shell:

```bash
sudo useradd -s /sbin/nologin ammar
```

## Command Explanation

```bash
sudo useradd -s /sbin/nologin ammar
```

Creates the user `ammar` and sets the login shell to `/sbin/nologin`.

The `-s` option means:

```text
-s = specify the user's login shell
```

Using `/sbin/nologin` prevents the account from opening an interactive shell session.

## Validation

Check the user ID and groups:

```bash
id ammar
```

Check the account entry:

```bash
getent passwd ammar
```

Expected result:

```text
ammar:x:<UID>:<GID>::/home/ammar:/sbin/nologin
```

The most important value is the last field:

```text
/sbin/nologin
```

This confirms the account was created with a non-interactive shell.

## Lessons Learned

- Service or application accounts often do not need interactive shell access.
- `/sbin/nologin` prevents a user from logging in interactively.
- `useradd -s <shell> <user>` creates a user with a specific login shell.
- `getent passwd <user>` is useful for validating a user’s shell.
- Always validate the final state after creating or modifying a user.

## Knowledge Check

### Question 1

Why would a service account use `/sbin/nologin` as its shell?

A. To prevent interactive login  
B. To give the user root access  
C. To delete the user's home directory  
D. To restart the SSH service  

**Answer:** A

### Question 2

Which command creates user `ammar` with a non-interactive shell?

A. `useradd ammar`  
B. `useradd -s /sbin/nologin ammar`  
C. `passwd ammar`  
D. `usermod -G /sbin/nologin ammar`  

**Answer:** B
