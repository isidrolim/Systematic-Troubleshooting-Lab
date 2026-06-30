# Task-004 – Create Service User Without Home Directory

## Scenario

The system admin team at xFusionCorp Industries required a new service user account for a tool implementation.

The account needed to exist on App Server 1, but it should not have an actual home directory created.

The requested account must be:

```text
Username: mark
Server: App Server 1
Home directory: not created
```

## Requirement

Create the user `mark` on App Server 1 without creating a home directory.

## Initial State

The user `mark` was not present on App Server 1.

The `/home/mark` directory was also not present.

## Troubleshooting Path

```text
Access App Server 1
↓
Confirm correct server
↓
Check if user mark already exists
↓
Check if /home/mark already exists
↓
Create user without creating a home directory
↓
Validate user exists
↓
Validate /home/mark was not created
```

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Check whether the user already exists:

```bash
id mark
```

or:

```bash
getent passwd mark
```

Check whether the home directory already exists:

```bash
ls -ld /home/mark
```

## First Finding

```text
User mark did not exist on App Server 1.
The /home/mark directory did not exist.
```

## Fix

Create the user without creating a home directory:

```bash
sudo useradd -M mark
```

## Command Explanation

```bash
sudo useradd -M mark
```

Creates the user `mark`.

The `-M` option means:

```text
-M = do not create the user's home directory
```

This is useful for service users that need a Linux account but do not need a personal home directory.

## Validation

Check that the user exists:

```bash
id mark
```

Check the account entry:

```bash
getent passwd mark
```

Example result:

```text
mark:x:<UID>:<GID>::/home/mark:/bin/bash
```

Check that the actual home directory was not created:

```bash
ls -ld /home/mark
```

Expected result:

```text
No such file or directory
```

You can also list `/home`:

```bash
ls -lah /home
```

Expected result:

```text
No mark directory should be present under /home.
```

## Important Note

The `/etc/passwd` entry may still show `/home/mark` as the configured home path.

That does not mean the directory exists.

The task requires that the home directory is not created, so the key validation is:

```bash
ls -ld /home/mark
```

If it returns `No such file or directory`, the user was created without an actual home directory.

## Lessons Learned

- `useradd` creates a Linux user.
- By default, some systems may create a home directory depending on configuration.
- `useradd -M <user>` creates a user without creating the home directory.
- `/etc/passwd` can show a home path even if the directory does not physically exist.
- Always validate the actual directory with `ls -ld /home/<user>`.

## Knowledge Check

### Question 1

Which option prevents `useradd` from creating a home directory?

A. `-m`  
B. `-M`  
C. `-s`  
D. `-u`  

**Answer:** B

### Question 2

Which command validates that `/home/mark` was not created?

A. `id mark`  
B. `getent passwd mark`  
C. `ls -ld /home/mark`  
D. `whoami`  

**Answer:** C
