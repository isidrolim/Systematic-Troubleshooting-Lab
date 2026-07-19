# Transfer a File to a Remote Server Using rsync

## Scenario

A Nautilus developer stored confidential data on the Jump Host in the Stratos Datacenter. Since developers do not have direct access to the application servers, the system administrator must securely transfer the file to **App Server 2**.

## Requirement

Copy the following file from the Jump Host:

```text
/tmp/nautilus.txt.gpg
```

to **App Server 2** under:

```text
/home/webapp/
```

---

## Initial State

Verify the source file exists on the Jump Host:

```bash
ls -l /tmp/nautilus.txt.gpg
```

Verify the destination directory exists on App Server 2:

```bash
ssh steve@stapp02
ls -ld /home/webapp
```

---

## Troubleshooting Path

### 1. Identify the symptom

The file transfer failed using `rsync`.

Error received:

```text
bash: line 1: rsync: command not found
rsync error: error in rsync protocol data stream (code 12)
```

---

### 2. Build the dependency path

```text
Source file exists
        ↓
SSH connectivity
        ↓
Authentication
        ↓
rsync installed on Jump Host
        ↓
rsync installed on App Server 2
        ↓
Destination directory exists
        ↓
File transfer succeeds
```

---

### 3. Verify the dependencies

Verified:

- ✅ Source file exists.
- ✅ SSH connection to App Server 2 works.
- ✅ Authentication succeeds.
- ✅ Destination directory exists.
- ✅ `rsync` installed on the Jump Host.

The transfer still failed.

---

## Verification Before Fix

The error message was the key evidence:

```text
bash: line 1: rsync: command not found
```

Since `rsync` communicates by launching an `rsync` process on the remote machine over SSH, this indicated that the **remote server** was missing the `rsync` package.

This explains why authentication succeeded but the transfer failed immediately afterward.

---

## Systematic Elimination

Instead of reinstalling `rsync` on the Jump Host, verify the remote server.

SSH into App Server 2:

```bash
ssh steve@stapp02
```

Verify whether `rsync` is installed:

```bash
which rsync
```

or

```bash
rsync --version
```

The command was not found, confirming the first failure.

---

## First Finding

The Jump Host had `rsync` installed, but **App Server 2 did not**.

Because `rsync` requires the utility on **both** the sender and receiver, the transfer could not begin.

---

## Fix

Install `rsync` on App Server 2:

```bash
sudo dnf install rsync -y
```

Then perform the file transfer from the Jump Host:

```bash
sudo rsync /tmp/nautilus.txt.gpg steve@stapp02:/home/webapp/
```

---

## Validation

Verify the file exists on App Server 2:

```bash
ssh steve@stapp02
ls -lah /home/webapp
```

Example output:

```text
-rw-r--r-- 1 steve steve 105 Jul 19 11:30 nautilus.txt.gpg
```

The KodeKloud validation completed successfully.

---

## Lessons Learned

- `rsync` requires the package to be installed on **both** the source and destination systems.
- SSH authentication succeeding does **not** guarantee the transfer will work.
- Read the error message carefully—the phrase **"rsync: command not found"** pointed directly to the missing dependency.
- Build and verify the dependency path before attempting fixes.

---

## Engineering Insight

This task demonstrated a common production troubleshooting principle:

> **Find the first failure instead of fixing everything.**

The dependency chain was:

```text
Jump Host
    │
    ├── Source file
    ├── SSH
    ├── Authentication
    ├── rsync
    │
    ▼
App Server 2
    ├── rsync
    ├── Destination directory
    ▼
Transfer completes
```

The first broken dependency was the missing `rsync` binary on the remote server.

This reinforces an important engineering lesson:

> Many client/server tools depend on software being available at **both ends** of the connection.

Examples include:

- `rsync`
- `scp`
- `sftp`
- `sshfs`
- Backup and synchronization tools

Always verify dependencies on both systems before troubleshooting network or permission issues.

---

## Knowledge Check

1. Why does `rsync` require the package on both the source and destination servers?
2. Which error message indicated the first failure in this task?
3. Why did SSH authentication succeed even though the transfer failed?
4. What would happen if the destination directory did not exist?
5. How is `rsync` different from `scp` in terms of synchronization and efficiency?
6. Why is identifying the **first failure** more effective than changing multiple components at once?
