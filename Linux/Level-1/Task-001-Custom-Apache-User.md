# Task-001 – Custom Apache User Setup

## Scenario

In response to heightened security concerns, the xFusionCorp Industries security team has opted for custom Apache users for their web applications. Each user is tailored specifically for an application, enhancing security measures. Your task is to create a custom Apache user according to the outlined specifications:

a. Create a user named kareem on App server 2 within the Stratos Datacenter.

b. Assign a unique UID 1235 and designate the home directory as /var/www/kareem.

Note: You can find the infrastructure details by clicking on the Details of all Users and Servers button on the top-right section of the page.

## Requirement

Create a user named `kareem` with:

- UID: `1235`
- Home directory: `/var/www/kareem`
- Server: App Server 2

## Symptom

The required application user did not exist yet on App Server 2.

## Dependency Path

```text
App Server 2 access
   ↓
User kareem does not already exist or must be configured correctly
   ↓
UID 1235 is available or assigned to kareem
   ↓
Home directory is /var/www/kareem
   ↓
User entry exists in /etc/passwd
   ↓
Validation confirms correct UID and home path

```
## Commands

hostname

id kareem

getent passwd 1235

## Solution

sudo useradd -u 1235 -d /var/www/kareem -m kareem
