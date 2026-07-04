# Copy Files by Owner and Preserve Directory Structure

## Scenario

Due to an accidental data mix-up, user data was unintentionally mingled on Nautilus App Server 1 under the `/home/usersdata` directory.

The production support team needed specific user-owned files to be filtered and relocated.

The requested action must be completed on App Server 1:

```text
Source directory: /home/usersdata
Owner: rose
Destination directory: /news
Requirement: copy files only, excluding directories
Preserve directory structure: yes
```

## Requirement

Locate all files owned by user `rose` inside `/home/usersdata` on App Server 1.

Copy those files to `/news` while preserving the original directory structure.

## Initial State

The source directory `/home/usersdata` existed.

The destination directory `/news` existed.

There were many files under `/home/usersdata` owned by user `rose`.

## Troubleshooting Path

```text
Access App Server 1
↓
Confirm correct server
↓
Confirm user rose exists
↓
Confirm source directory exists
↓
Confirm destination directory exists
↓
Find files owned by rose under /home/usersdata
↓
Copy only those files to /news
↓
Preserve the original directory structure
↓
Validate copied file count and destination path
```

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Check whether the user exists:

```bash
getent passwd rose
```

Check the source directory:

```bash
ls -ld /home/usersdata
```

Check the destination directory:

```bash
ls -ld /news
```

Find files owned by `rose` under `/home/usersdata`:

```bash
find /home/usersdata -type f -user rose
```

Count the matching source files:

```bash
find /home/usersdata -type f -user rose | wc -l
```

## First Finding

```text
The user rose existed on App Server 1.
The source directory /home/usersdata existed.
The destination directory /news existed.
There were 1880 files owned by rose under /home/usersdata.
```

## Fix

Copy all files owned by `rose` from `/home/usersdata` to `/news` while preserving the directory structure:

```bash
sudo find /home/usersdata -type f -user rose -exec cp --parents {} /news \;
```

## Command Explanation

```bash
sudo find /home/usersdata -type f -user rose -exec cp --parents {} /news \;
```

Searches under `/home/usersdata` and copies only matching files to `/news`.

Important options:

```text
/home/usersdata = source directory to search
-type f         = match files only, excluding directories
-user rose     = match files owned by user rose
-exec          = run a command for each matched file
{}             = placeholder for each matched file
cp --parents   = copy the file and preserve its directory path
/news          = destination directory
\;             = end of the -exec command
```

The `--parents` option preserves the source path.

Example:

```text
Original file:
/home/usersdata/wp-includes/comment.php

Copied file:
/news/home/usersdata/wp-includes/comment.php
```

## Validation

Count the source files owned by `rose`:

```bash
find /home/usersdata/ -type f -user rose | wc -l
```

Result:

```text
1880
```

Count the copied files under `/news`:

```bash
find /news/home/usersdata/ -type f | wc -l
```

Result:

```text
1880
```

Check the copied directory structure:

```bash
ls -lah /news/
```

Expected result:

```text
/news/home/usersdata
```

Check copied files:

```bash
ls -lah /news/home/usersdata/
```

Expected result:

```text
Files and directories from /home/usersdata are present under /news/home/usersdata.
```

## Lessons Learned

- `find` is useful for locating files based on owner, type, name, size, and other attributes.
- `-type f` ensures only regular files are matched.
- `-user <username>` filters files by owner.
- `cp --parents` copies files while preserving their original directory path.
- For file-copy tasks, validation should include both file count and destination structure.
- Matching the source and destination file counts helps confirm the copy was successful.

## Knowledge Check

### Question 1

Which `find` option ensures that only files are selected and directories are excluded?

A. `-name`  
B. `-type f`  
C. `-user`  
D. `-exec`  

**Answer:** B

### Question 2

Which command copies files owned by `rose` from `/home/usersdata` to `/news` while preserving directory structure?

A. `cp /home/usersdata /news`  
B. `find /home/usersdata -user rose -exec cp {} /news \;`  
C. `find /home/usersdata -type f -user rose -exec cp --parents {} /news \;`  
D. `mv /home/usersdata /news`  

**Answer:** C
