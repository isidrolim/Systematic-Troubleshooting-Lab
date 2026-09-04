# Task-015 – Timezone Alignment

## Skills Practiced

- Linux Time Management
- timedatectl
- systemd
- Timezone Configuration
- Multi-Server Administration
- Validation

---

## Scenario

During a daily standup, the system administration team discovered that timezone settings across the Nautilus Application Servers were inconsistent with the local timezone of the Stratos Datacenter.

All application servers must use the same timezone to maintain consistent timestamps across applications, logs, scheduled jobs, and system events.

---

## Requirement

Configure all Nautilus Application Servers to use:

```text
Australia/Hobart
```

Target servers:

```text
stapp01
stapp02
stapp03
```

---

## Initial State

Before making any changes, verify the current timezone on each App server.

Confirm the correct server:

```bash
hostname
```

Check the current timezone:

```bash
timedatectl
```

A more focused check can also be used:

```bash
timedatectl show -p Timezone
```

---

## Troubleshooting Path

```text
Access App Server
↓
Confirm correct server
↓
Check current timezone
↓
Compare with required timezone
↓
Set Australia/Hobart
↓
Validate timezone
↓
Repeat on all App servers
```

---

## Verification Before Fix

Run on each App server:

```bash
hostname
timedatectl
```

The required final timezone is:

```text
Australia/Hobart
```

---

## First Finding

The application servers needed their timezone configuration aligned with the Stratos Datacenter timezone.

Required timezone:

```text
Australia/Hobart
```

---

## Fix

Set the timezone:

```bash
sudo timedatectl set-timezone Australia/Hobart
```

Apply the change on:

```text
stapp01
stapp02
stapp03
```

---

## Validation

Verify the timezone after the change:

```bash
timedatectl
```

Expected result should include:

```text
Time zone: Australia/Hobart
```

A focused validation can also be performed:

```bash
timedatectl show -p Timezone
```

Expected:

```text
Timezone=Australia/Hobart
```

Repeat the validation on all three App servers.

---

## Lessons Learned

- `timedatectl` manages system time and timezone settings on systemd-based Linux systems.
- `timedatectl set-timezone` changes the system timezone.
- Always verify the current timezone before making changes.
- Timezone consistency is important across multiple application servers.
- Incorrect timezone settings can make troubleshooting logs, scheduled jobs, and distributed applications difficult.
- Multi-server configuration tasks must be validated individually on every required server.

---

## Engineering Insight

Timezone configuration may appear simple, but inconsistent time settings across servers can create difficult production problems.

For example:

```text
Application Server 1 → 10:00
Application Server 2 → 18:00
Application Server 3 → 02:00
```

If an incident occurs, comparing logs across these systems becomes difficult.

Consistent timezone configuration provides:

```text
Consistent timestamps
↓
Reliable log correlation
↓
Easier troubleshooting
↓
More accurate incident timelines
```

The engineering workflow remains the same:

```text
Verify current state
↓
Compare with requirement
↓
Change only what is necessary
↓
Validate final state
```

---

## Knowledge Check

### Question 1

Which command displays the current system timezone?

A. `datectl`

B. `timedatectl`

C. `timezone`

D. `systemctl timezone`

**Answer:** B

### Question 2

Which command sets the timezone to `Australia/Hobart`?

A.

```bash
timedatectl Australia/Hobart
```

B.

```bash
sudo timedatectl set-timezone Australia/Hobart
```

C.

```bash
sudo systemctl timezone Australia/Hobart
```

D.

```bash
sudo date --timezone Australia/Hobart
```

**Answer:** B
