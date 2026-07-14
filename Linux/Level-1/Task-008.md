# Archive Directory and Transfer

## Scenario

A developer requested a backup copy of the `/data/ammar` directory on the Jump Host.

The directory needed to be compressed into a `tar.gz` archive and stored in the `/home` directory.

The requested operation must be completed on the Jump Host.

```text
Source directory : /data/ammar
Archive name     : ammar.tar.gz
Destination      : /home
```

## Requirement

Create a compressed archive named `ammar.tar.gz` from `/data/ammar` and place the archive in `/home`.

## Initial State

The `/data/ammar` directory already existed.

The `/home` directory already existed.

The archive `ammar.tar.gz` did not exist.

## Troubleshooting Path

```text
Access Jump Host
↓
Confirm correct server
↓
Verify source directory exists
↓
Verify destination directory exists
↓
Create compressed archive
↓
Store archive in /home
↓
Validate archive exists
```

## Verification Before Fix

Confirm current server:

```bash
hostname
```

Confirm current working directory:

```bash
pwd
```

Verify the source directory:

```bash
ls -lah /data/ammar
```

Verify the destination directory:

```bash
ls -ld /home
```

## First Finding

```text
The source directory existed.
The destination directory existed.
The archive file did not exist.
```

## Fix

Create the compressed archive:

```bash
sudo tar -czf /home/ammar.tar.gz /data/ammar
```

## Command Explanation

```bash
sudo tar -czf /home/ammar.tar.gz /data/ammar
```

Breakdown:

```text
tar   = archive utility

-c    = create a new archive

-z    = compress using gzip

-f    = specify the archive filename

/home/ammar.tar.gz
      = output archive

/data/ammar
      = source directory
```

Think of the command as:

```text
Create
↓
Compress
↓
Store archive here
↓
Using this source
```

## Validation

Verify the archive exists:

```bash
ls -lah /home
```

Expected:

```text
ammar.tar.gz
```

Verify archive contents without extracting:

```bash
tar -tzf /home/ammar.tar.gz
```

Expected:

```text
Shows files stored inside the archive.
```

## Warning Encountered

During archive creation, the following message appeared:

```text
tar: Removing leading '/' from member names
```

This is **not an error**.

`tar` removes the leading `/` from stored file paths so the archive can be extracted safely on another system without overwriting files using absolute paths.

## Lessons Learned

- `tar` creates archives.
- `-c` creates a new archive.
- `-z` compresses using gzip.
- `-f` specifies the archive filename.
- Always identify the source and destination before creating an archive.
- Red text in terminal is not always an error. Read the actual message before troubleshooting.
- Permission errors occur because file creation depends on the destination directory permissions.

## Linux Grammar

Instead of memorizing:

```text
tar -czf
```

Think:

```text
tar
↓

Create
↓

Compress

↓

Output archive

↓

Source
```

## Knowledge Check

### Question 1

What does the `-f` option specify?

A. Source directory

B. Archive filename

C. Compression level

D. File permissions

**Answer:** B

---

### Question 2

The message below appeared while creating the archive:

```text
tar: Removing leading '/' from member names
```

What does it mean?

A. The archive creation failed.

B. `tar` is removing dangerous absolute paths from the archive for safer extraction.

C. The source directory does not exist.

D. The archive is corrupted.

**Answer:** B
