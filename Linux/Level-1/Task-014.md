# Task-014 – Configure Default Runlevel for GUI Boot

## Skills Practiced

- systemd
- Linux Boot Targets
- Runlevels
- systemctl
- Multi-Server Administration
- Validation

---

## Scenario

New tools installed on the application servers require graphical user interface (GUI) functionality.

The system administration team therefore needs all App servers in the Stratos Datacenter configured to boot into graphical mode by default.

The servers must **not be rebooted** as part of the change.

---

## Requirement

Configure the default boot target on all App servers:

```text
stapp01
stapp02
stapp03
```

Required default target:

```text
graphical.target
```

Do not reboot the servers.

---

## Initial State

Before making changes, confirm the correct server:

```bash
hostname
```

Check the current default systemd target:

```bash
systemctl get-default
```

---

## Troubleshooting Path

```text
Access App Server
↓
Confirm correct server
↓
Check current default target
↓
Determine required GUI target
↓
Set graphical.target as default
↓
Verify new default target
↓
Repeat on all App servers
```

---

## Verification Before Fix

Run on each App server:

```bash
hostname
systemctl get-default
```

The required final state is:

```text
graphical.target
```

---

## First Finding

The App servers needed their default systemd boot target configured for graphical mode.

On modern systemd-based Linux systems, traditional runlevels map approximately to:

```text
Runlevel 3
↓
multi-user.target
↓
Command-line multi-user environment
```

```text
Runlevel 5
↓
graphical.target
↓
Graphical multi-user environment
```

---

## Fix

Set the default target:

```bash
sudo systemctl set-default graphical.target
```

Apply the command on:

```text
stapp01
stapp02
stapp03
```

Do not reboot the servers.

---

## Validation

After changing the target, verify:

```bash
systemctl get-default
```

Expected result:

```text
graphical.target
```

Perform the same validation on all three App servers.

---

## Lessons Learned

- Modern Linux distributions using systemd use **targets** instead of traditional SysV runlevels.
- `multi-user.target` is roughly equivalent to the traditional runlevel 3.
- `graphical.target` is roughly equivalent to the traditional runlevel 5.
- `systemctl get-default` displays the target used during normal system boot.
- `systemctl set-default graphical.target` changes the default boot target.
- Changing the default target does not require an immediate reboot.
- Always verify the existing state before changing system configuration.
- For multi-server tasks, configure and validate every required server.

---

## Engineering Insight

Changing the **default boot target** and changing the **currently running target** are different operations.

```text
systemctl set-default
↓
Changes future boot behavior
```

while:

```text
systemctl isolate
↓
Changes the current running target
```

The requirement only asks for future GUI boot behavior and explicitly prohibits rebooting.

Therefore, the safest change is to modify the default target without changing the current operating state.

---

## Knowledge Check

1. Which systemd target corresponds approximately to traditional runlevel 5?

   A. `rescue.target`  
   B. `multi-user.target`  
   C. `graphical.target`  
   D. `emergency.target`

   **Answer: C**

2. Which command verifies the system's configured default boot target?

   A. `systemctl status`  
   B. `systemctl get-default`  
   C. `systemctl isolate`  
   D. `systemctl list-units`

   **Answer: B**
