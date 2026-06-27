# Task-001 – Custom Apache User Setup

## Scenario

A custom Apache application user is required on App Server 2 for security isolation.

The requested account must be:

```text
Username: kareem
UID: 1235
Home directory: /var/www/kareem
Server: App Server 2
```

## Requirement

Create the user `kareem` with UID `1235` and home directory `/var/www/kareem`.

## Initial State

The user `kareem` was not present on App Server 2.

## Troubleshooting Path

```text
Confirm correct server
↓
Check if user kareem already exists
↓
Check if UID 1235 is already assigned
↓
Create user with required UID and home directory
↓
Validate final account details
```

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Check whether the user already exists:

```bash
id kareem
```

Check whether UID `1235` is already assigned:

```bash
getent passwd 1235
```

## First Finding

```text
User kareem did not exist.
UID 1235 was available.
```

## Fix

```bash
sudo useradd -u 1235 -d /var/www/kareem -m kareem
```

## Validation

Check the user ID and groups:

```bash
id kareem
```

Check the account entry:

```bash
getent passwd kareem
```

Check the home directory:

```bash
ls -ld /var/www/kareem
```

Expected result:

```text
User kareem exists with UID 1235 and home directory /var/www/kareem.
```

## Lessons Learned

- User-management tasks should still be verified before making changes.
- `id <user>` confirms whether a user exists.
- `getent passwd <uid>` checks if a UID is already assigned.
- `useradd -u <uid> -d <home> -m <user>` creates a user with a custom UID and home directory.

## Knowledge Check

### Question 1

Before creating a Linux user with a custom UID, what should you verify first?

A. Whether the UID is already assigned  
B. Whether Apache is running  
C. Whether `/tmp` is empty  
D. Whether the server can access the internet  

**Answer:** A

### Question 2

Which command creates user `kareem` with UID `1235` and home directory `/var/www/kareem`?

A. `useradd kareem`  
B. `useradd -u 1235 -d /var/www/kareem -m kareem`  
C. `passwd kareem`  
D. `mkdir /var/www/kareem`  

**Answer:** B
