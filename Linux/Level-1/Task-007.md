# Disable Direct Root SSH Login

## Scenario

Following security audits, the xFusionCorp Industries security team required a new SSH security control across all App servers in the Stratos Datacenter.

Direct SSH login as the `root` user must be disabled on all App servers.

The requested configuration must be applied on:

```text
stapp01
stapp02
stapp03
```

## Requirement

Disable direct root SSH login on all App servers.

The SSH daemon configuration must include:

```text
PermitRootLogin no
```

## Initial State

The SSH daemon configuration needed to be reviewed and updated on each App server.

The main SSH daemon configuration file is:

```text
/etc/ssh/sshd_config
```

There may also be additional SSH configuration files under:

```text
/etc/ssh/sshd_config.d/
```

## Troubleshooting Path

```text
Access each App server
↓
Confirm correct server
↓
Locate SSH daemon configuration
↓
Check current PermitRootLogin setting
↓
Back up the SSH configuration
↓
Update PermitRootLogin to no
↓
Validate SSH configuration syntax
↓
Restart sshd service
↓
Confirm direct root SSH login is disabled
```

## Verification Before Fix

Confirm the current server:

```bash
hostname
```

Check the current SSH root login setting:

```bash
grep -nE '^#?PermitRootLogin' /etc/ssh/sshd_config
```

Check for additional SSH configuration files:

```bash
ls -l /etc/ssh/sshd_config.d/
```

Optional broader check:

```bash
grep -RniE '^#?PermitRootLogin' /etc/ssh/sshd_config /etc/ssh/sshd_config.d/
```

## First Finding

```text
The SSH daemon configuration needed to enforce PermitRootLogin no on all App servers.
```

## Fix

Before editing, create a backup of the SSH daemon configuration:

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
```

Edit the SSH daemon configuration:

```bash
sudo vi /etc/ssh/sshd_config
```

Set or update the following directive:

```text
PermitRootLogin no
```

If using `sed`, the setting can be updated with:

```bash
sudo sed -i 's/^#\?\s*PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
```

## Validate Configuration Before Restart

Test the SSH daemon configuration syntax:

```bash
sudo sshd -t
```

Expected result:

```text
No output means the SSH configuration syntax is valid.
```

## Restart SSH Service

Restart the SSH daemon:

```bash
sudo systemctl restart sshd
```

If the system uses a different service name, check with:

```bash
systemctl status sshd
```

or:

```bash
systemctl status ssh
```

## Validation

Confirm the final setting:

```bash
grep -nE '^PermitRootLogin' /etc/ssh/sshd_config
```

Expected result:

```text
PermitRootLogin no
```

Confirm the SSH daemon is running:

```bash
sudo systemctl status sshd
```

Optional validation from the jump host:

```bash
ssh root@stapp01
```

Expected result:

```text
Direct root SSH login should be denied.
```

Repeat validation for:

```text
stapp01
stapp02
stapp03
```

## Important Note

The file:

```text
/etc/ssh/sshd_config
```

is the SSH daemon configuration file.

The service is not called `sshd_config`.

The service to restart is usually:

```text
sshd
```

Correct:

```bash
sudo systemctl restart sshd
```

Incorrect:

```bash
sudo systemctl restart sshd_config
```

## Lessons Learned

- SSH daemon settings are usually configured in `/etc/ssh/sshd_config`.
- `PermitRootLogin no` disables direct SSH login as the root user.
- Always create a backup before editing critical configuration files.
- Always test SSH configuration syntax with `sshd -t` before restarting the service.
- Configuration files and services are different things.
- After changing SSH settings, restart or reload the SSH daemon for changes to apply.
- Security tasks should be applied consistently across all required servers.

## Knowledge Check

### Question 1

Which SSH daemon directive disables direct root SSH login?

A. `PasswordAuthentication no`  
B. `PermitRootLogin no`  
C. `AllowUsers no`  
D. `RootLogin disabled`  

**Answer:** B

### Question 2

Which command tests the SSH daemon configuration syntax before restarting the service?

A. `sudo sshd -t`  
B. `sudo systemctl test sshd`  
C. `sudo ssh -reload`  
D. `sudo cat /etc/ssh/sshd_config`  

**Answer:** A
