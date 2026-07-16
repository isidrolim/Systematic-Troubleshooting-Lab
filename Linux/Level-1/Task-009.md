# Grant Execute Permission to All Users

## Skills Practiced

- Linux File Permissions
- chmod
- Executable Files
- Linux Permission Classes
- Validation
- Linux Permission Grammar

---

## Scenario

The xFusionCorp Industries backup automation team deployed a new shell script named `xfusioncorp.sh` to App Server 3.

The script existed, but it was not executable.

The security team required that **all users** be able to execute the script.

The requested change must be completed on:

```text
Server : App Server 3
File   : /tmp/xfusioncorp.sh
```

---

## Requirement

Grant execute permission to **all users** for:

```text
/tmp/xfusioncorp.sh
```

---

## Initial State

The script already existed under:

```text
/tmp/xfusioncorp.sh
```

However, it did not have the required execute permissions for all users.

---

## Troubleshooting Path

```text
Access App Server 3
↓

Confirm correct server

↓

Verify script exists

↓

Inspect current permissions

↓

Determine required permission

↓

Grant execute permission

↓

Validate final permissions
```

---

## Verification Before Fix

Confirm current server:

```bash
hostname
```

Verify the script exists:

```bash
ls -lah /tmp/
```

Inspect current permissions:

```bash
ls -l /tmp/xfusioncorp.sh
```

---

## First Finding

```text
The script existed.

Execute permission for all users was not present.
```

---

## Fix

Grant execute permission to everyone:

```bash
sudo chmod 555 /tmp/xfusioncorp.sh
```

---

## Command Explanation

```bash
sudo chmod 555 /tmp/xfusioncorp.sh
```

Breakdown:

```text
chmod

↓

Change file permissions

555

↓

Owner  = r-x

Group  = r-x

Others = r-x
```

Numeric permissions:

| Value | Meaning |
|-------:|---------|
| 4 | Read |
| 2 | Write |
| 1 | Execute |

Therefore:

```text
5

↓

4 + 1

↓

Read + Execute
```

So:

```text
555

↓

Owner   r-x

Group   r-x

Others  r-x
```

Every user can execute the script.

---

## Validation

Verify final permissions:

```bash
ls -l /tmp/xfusioncorp.sh
```

Expected:

```text
-r-xr-xr-x
```

---

## Linux Concept Learned

### Topic

Linux File Permissions

### Key Idea

Linux evaluates permissions using three permission classes:

```text
Owner

↓

Group

↓

Others
```

Each class has three possible permissions:

```text
r

↓

Read

w

↓

Write

x

↓

Execute
```

---

### Linux Permission Grammar

Instead of memorizing:

```bash
chmod 555
```

Think:

```text
Owner

↓

Read

Execute

Group

↓

Read

Execute

Others

↓

Read

Execute
```

---

### Understanding `chmod +x`

During this challenge, we discussed an important Linux concept.

Many beginners think:

```text
chmod +x

↓

Adds execute only for the owner.
```

Actually:

```text
chmod +x

↓

Adds execute permission to all permission classes
```

Equivalent to:

```bash
chmod a+x
```

Where:

```text
a

↓

All users

↓

Owner

Group

Others
```

Example:

Current permissions:

```text
-rw-r--r--
```

After:

```bash
chmod +x file
```

Result:

```text
-rwxr-xr-x
```

---

### Files vs Directories

One of the most important Linux concepts:

For files:

```text
r

↓

Read file contents

w

↓

Modify file

x

↓

Execute file
```

For directories:

```text
r

↓

List directory contents

w

↓

Create/Delete entries

x

↓

Enter (traverse) directory
```

The meaning of **execute** changes depending on whether the object is a file or a directory.

---

## Lessons Learned

- `chmod` changes Linux permissions.
- Numeric permissions are easier to read once you understand the values.
- `555` means everyone gets Read and Execute permissions.
- `chmod +x` adds execute permission to all permission classes unless a specific class is provided.
- The owner of a file is **not** determined by `sudo`; `sudo` only grants permission to modify the file.
- Always inspect current permissions before changing them.
- Validate permission changes using `ls -l`.

---

## Knowledge Check

### Question 1

What does the permission value **5** represent?

A. Read only

B. Read + Execute

C. Read + Write

D. Execute only

**Answer:** B

---

### Question 2

Given the current permissions:

```text
-rw-r--r--
```

After running:

```bash
chmod +x file
```

What will the new permissions be?

A.

```text
-rwx------
```

B.

```text
-rwxr-xr-x
```

C.

```text
-rw-r--r-x
```

D.

```text
-rwx--x--x
```

**Answer:** B
