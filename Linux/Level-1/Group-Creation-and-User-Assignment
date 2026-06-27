# Task-002 – Group Creation and User Assignment

## Scenario

The system admin team at xFusionCorp Industries implemented group-based access control for SFTP users.

A common group must exist across all App servers, and the required user must be added to that group.

The requested configuration must be applied on all App servers in the Stratos Datacenter:

```text
Group: nautilus_sftp_users
User: jarod
Servers: stapp01, stapp02, stapp03
```

## Requirement

Create the group `nautilus_sftp_users` on all App servers.

Add the user `jarod` to the `nautilus_sftp_users` group on all App servers.

If the user `jarod` does not exist, create the user first.

## Initial State

The group `nautilus_sftp_users` was not present on the App servers.

The user `jarod` was also not present on the App servers.

## Troubleshooting Path

```text
Access each App server
↓
Check if group nautilus_sftp_users exists
↓
Create group if missing
↓
Check if user jarod exists
↓
Create user if missing
↓
Add jarod to nautilus_sftp_users
↓
Validate group membership on each App server
```

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Check whether the group already exists:

```bash
getent group nautilus_sftp_users
```

Check whether the user already exists:

```bash
id jarod
```

These checks were performed on:

```text
stapp01
stapp02
stapp03
```

## First Finding

```text
The group nautilus_sftp_users did not exist on the App servers.
The user jarod did not exist on the App servers.
```

## Fix

Run the following commands on each App server:

```bash
sudo groupadd nautilus_sftp_users
sudo useradd jarod
sudo usermod -aG nautilus_sftp_users jarod
```

The commands were applied on:

```text
stapp01
stapp02
stapp03
```

## Command Explanation

```bash
sudo groupadd nautilus_sftp_users
```

Creates the required group.

```bash
sudo useradd jarod
```

Creates the required user.

```bash
sudo usermod -aG nautilus_sftp_users jarod
```

Adds `jarod` to the supplementary group `nautilus_sftp_users`.

The `-aG` option is important:

```text
-a = append the user to the group
-G = specify supplementary group
```

Using `-aG` prevents accidentally replacing the user's existing supplementary groups.

## Validation

Validate the group:

```bash
getent group nautilus_sftp_users
```

Expected result:

```text
nautilus_sftp_users:x:<GID>:jarod
```

Validate the user and group membership:

```bash
id jarod
```

Expected result should show `jarod` as a member of `nautilus_sftp_users`.

Example:

```text
uid=<UID>(jarod) gid=<GID>(jarod) groups=<GID>(jarod),<GID>(nautilus_sftp_users)
```

Validation was completed on:

```text
stapp01
stapp02
stapp03
```

## Lessons Learned

- Group-based access control allows permissions to be managed by group membership instead of individual users.
- `getent group <groupname>` checks whether a group exists.
- `id <username>` checks whether a user exists and shows group membership.
- `groupadd` creates a new Linux group.
- `useradd` creates a new Linux user.
- `usermod -aG <group> <user>` adds an existing user to a supplementary group.
- Always use `-aG`, not only `-G`, when adding a user to a group to avoid overwriting existing supplementary groups.
- For multi-server tasks, the same validation must be performed on every target server.

## Knowledge Check

### Question 1

Why should you check whether `nautilus_sftp_users` exists before creating it?

A. To verify whether the group already exists and avoid unnecessary errors  
B. To restart the SSH service  
C. To check disk usage  
D. To confirm Apache is running  

**Answer:** A

### Question 2

Which command adds user `jarod` to the `nautilus_sftp_users` group without removing existing supplementary groups?

A. `usermod -G nautilus_sftp_users jarod`  
B. `usermod -aG nautilus_sftp_users jarod`  
C. `groupadd jarod nautilus_sftp_users`  
D. `useradd -G jarod nautilus_sftp_users`  

**Answer:** B
