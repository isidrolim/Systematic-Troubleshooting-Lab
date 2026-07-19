# Replace Text in an XML File

## Scenario
A Nautilus administrator needs to update an XML configuration file. Every occurrence of the word **About** must be replaced with **Maritime** in `/root/nautilus.xml`.

## Requirement
Replace all occurrences of:

- **About** → **Maritime**

in:

```text
/root/nautilus.xml
```

---

## Initial State

Before making any changes, verify the target text exists:

```bash
sudo grep -n 'About' /root/nautilus.xml
```

Example output:

```text
310:<about>About</about>
322:<about>About</about>
...
```

This confirms:
- The file is accessible.
- The target string exists.
- Multiple occurrences need to be replaced.

---

## Troubleshooting Path

### 1. Identify the symptom

The XML file still contains the old text:

```text
About
```

---

### 2. Understand the requirement

The task requires replacing **every** occurrence, not just the first one.

---

### 3. Protect the original file

Before editing a production file, create a backup.

```bash
sudo cp /root/nautilus.xml /root/nautilus.xml.bak
```

This provides an immediate rollback point if something goes wrong.

---

### 4. Apply the replacement

```bash
sudo sed -i 's/About/Maritime/g' /root/nautilus.xml
```

Command breakdown:

- `sudo` → Execute with elevated privileges.
- `sed` → Stream editor used for text manipulation.
- `-i` → Edit the file **in place**.
- `s` → Substitute.
- `About` → Text to search.
- `Maritime` → Replacement text.
- `g` → Replace **all** occurrences on each line.
- `/root/nautilus.xml` → Target file.

---

## Verification Before Fix

Verify that the original text exists:

```bash
sudo grep -n 'About' /root/nautilus.xml
```

---

## Systematic Elimination

Instead of editing manually:

- Verify the target exists.
- Back up the file.
- Perform an automated replacement.
- Validate the results.

This minimizes the risk of human error.

---

## First Finding

The XML file contained multiple instances of:

```text
About
```

A global replacement was required.

---

## Fix

```bash
sudo cp /root/nautilus.xml /root/nautilus.xml.bak

sudo sed -i 's/About/Maritime/g' /root/nautilus.xml
```

---

## Validation

Verify the replacement:

```bash
sudo grep -n 'Maritime' /root/nautilus.xml
```

Example output:

```text
310:<about>Maritime</about>
322:<about>Maritime</about>
334:<about>Maritime</about>
...
```

The KodeKloud validation completed successfully.

---

## Lessons Learned

- `sed` is a powerful stream editor for automated text replacement.
- `-i` modifies the file directly.
- `s` stands for **Substitute**.
- `g` performs a **global** replacement on each line.
- Linux string matching is case-sensitive by default.
- Always verify the target text before modifying a file.
- Creating a backup before editing production configuration files is a best practice.

---

## Engineering Insight

This task reinforced an important production engineering workflow:

1. Verify the current state.
2. Create a backup.
3. Apply the change.
4. Validate the result.

Rather than memorizing the command, understanding its grammar makes it reusable:

```text
sed
│
├── -i        → Edit the file in place
├── s         → Substitute
├── About     → Search text
├── Maritime  → Replacement text
├── g         → Replace all occurrences
└── File      → Target file
```

This same approach applies when modifying configuration files such as:

- `/etc/ssh/sshd_config`
- `/etc/fstab`
- Nginx configuration
- Apache configuration
- Kubernetes YAML manifests

---

## Knowledge Check

1. What does the `-i` option do in `sed`?
2. What does the `s` command represent?
3. What does the `g` flag do?
4. Why is `About` different from `about` in this command?
5. Why is creating a backup before editing a production file considered a best practice?
6. If you needed to restore the original file, which command would you use?
