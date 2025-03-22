<div class="flex-container">
        <img src="https://github.com/ProfessionalLinuxUsersGroup/img/blob/main/Assets/Logos/ProLUG_Round_Transparent_LOGO.png?raw=true" width="64" height="64"></img>
    <p>
        <h1>Unit 1 Lab - Database Remediation</h1>
    </p>
</div>


## Module 1: Exploring System Information

### Exercise 1.1: Familiarizing ourselves with the System

```bash
mount | grep -i noexec

mount | grep -i nodev

mount | grep -i nosuid

# Approximately how many of your mounted filesystems have each of these values?
```

### Exercise 1.2: Checking Mounted Systems

```bash
sysctl -a | grep -i ipv4

sysctl -a | grep -i ipv6

# How many of each are there?
```

```bash
sysctl -a | grep -i ipv4 | grep -i forward

# Does IPv4 forward on interfaces?
```

```bash
lsmod | grep -i tables

# What type of tables exist?
```

## Module 2: PreLAB

1. Download the STIG Viewer 2.18 from - <https://public.cyber.mil/stigs/downloads/>
2. Download the STIG for Mariadb and the import it into your STIG viewer.

## Module 3: Lab

This lab is designed to have the engineer practice securing a Linux server or service
against a set of configuration standards.
These standards are sometimes called benchmarks, checklists, or guidelines.  
The engineer will be using STIG Viewer 2.18 to complete this lab.

### MariaDB Service configuration:

1. Connect to a hammer server.
2. Install MariaDB.

```bash
dnf install mariadb-server

# Ensure that it is running

systemctl start mariadb

systemctl status mariadb

ss -ntulp | grep 3306
```

- Check and remediate v-253666 STIG.

  - What is the problem?
  - What is the fix?
  - What type of control is being implemented?
  - Is it set properly on your system?

  ```sql
  -- Connect to `mariadb` locally.

  SELECT user, max_user_connections FROM mysql.user;

  -- Can you remediate this finding?
  ```

- Check and remediate `v-253677 STIG`
  - What is the problem?
  - What is the fix?
  - What type of control is being implemented?
  - Is it set properly on your system?
- Check and remediate `v-253678 STIG`
  - What is the problem?
  - What is the fix?
  - What type of control is being implemented?
  - Is it set properly on your system?
- Check and remediate `v-253734 STIG`
  - What is the problem?
  - What is the fix?
  - What type of control is being implemented?
  - Is it set properly on your system?
