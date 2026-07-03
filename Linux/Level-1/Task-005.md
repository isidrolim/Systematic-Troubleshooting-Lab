# Create User with Expiry Date

## Scenario

The system admin team at xFusionCorp Industries required a new user account with a specific account expiry date.

This is useful when a user account is temporary and should automatically become inactive after a certain date.

The requested account must be created on App Server 1:

```text
Username: ravi
Server: App Server 1
Expiry date: 2027-02-17
```

## Requirement

Create the user `ravi` on App Server 1 with an account expiry date of `2027-02-17`.

## Initial State

The user `ravi` was not present on App Server 1.

## Troubleshooting Path

```text
Access App Server 1
↓
Confirm correct server
↓
Check if user ravi already exists
↓
Create user with expiry date
↓
Validate user exists
↓
Validate account expiry date
```

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Check whether the user already exists:

```bash
id ravi
```

or:

```bash
getent passwd ravi
```

## First Finding

```text
User ravi did not exist on App Server 1.
```

## Fix

Create the user with the required expiry date:

```bash
sudo useradd ravi -e 2027-02-17
```

## Command Explanation

```bash
sudo useradd ravi -e 2027-02-17
```

Creates the user `ravi` and sets the account expiry date to `2027-02-17`.

The `-e` option means:

```text
-e = set the account expiry date
```

After the expiry date, the account becomes inactive and can no longer be used for login.

## Validation

Check that the user exists:

```bash
id ravi
```

Check the account entry:

```bash
getent passwd ravi
```

Example result:

```text
ravi:x:<UID>:<GID>::/home/ravi:/bin/bash
```

Check the account expiry information:

```bash
sudo chage -l ravi
```

Expected important value:

```text
Account expires : Feb 17, 2027
```

## Lessons Learned

- `useradd` creates a new Linux user.
- `useradd -e <date>` creates a user with an account expiry date.
- Expiry dates are useful for temporary accounts.
- `id <user>` confirms that the user exists.
- `getent passwd <user>` checks the account entry.
- `chage -l <user>` shows password aging and account expiry information.
- Always validate the actual expiry date after creating the user.

## For validation in future expiry-date tasks, the best command to remember is:
`sudo chage -l username`

## Knowledge Check

### Question 1

Which option is used with `useradd` to set an account expiry date?

A. `-u`  
B. `-d`  
C. `-e`  
D. `-s`  

**Answer:** C

### Question 2

Which command creates user `ravi` with an expiry date of `2027-02-17`?

A. `useradd ravi`  
B. `useradd -e 2027-02-17 ravi`  
C. `useradd ravi -e 2027-02-17`  
D. Both B and C  

**Answer:** D
