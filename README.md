
# Linux Interview Fundamentals & Common Tricky Questions

This guide covers many essential concepts and commands expected from a Linux administrator. You’ll find detailed sections on the filesystem, permissions, process management, shell scripting, networking, text processing, package management, job scheduling, and more. In addition, a dedicated Q&A section lists commonly asked interview questions with clear answers and examples.

---

## Table of Contents

1. [Introduction](#introduction)
2. [Filesystem Hierarchy & File Management](#filesystem-hierarchy--file-management)
3. [File Permissions, Ownership & Links](#file-permissions-ownership--links)
4. [Commonly Used Commands & Text Processing](#commonly-used-commands--text-processing)
5. [Process, Job & Resource Management](#process-job--resource-management)
6. [Shell Scripting Essentials](#shell-scripting-essentials)
7. [Networking Fundamentals](#networking-fundamentals)
8. [Package Management & Software Installation](#package-management--software-installation)
9. [Job Scheduling & Cron vs. systemd Timers](#job-scheduling--cron-vs-systemd-timers)
10. [Basic Troubleshooting & Log Management](#basic-troubleshooting--log-management)
11. [Interview Questions & Answers](#interview-questions--answers)
12. [Conclusion](#conclusion)

---

## 1. Introduction

This guide is intended for candidates and administrators looking to refresh their Linux fundamentals and prepare for interviews. It covers the most commonly tested topics, reinforces them with examples, and highlights tricky interview questions along with sample answers.

---

## 2. Filesystem Hierarchy & File Management

### Filesystem Basics

- **Linux Standard FHS:**  
  Understand that directories such as `/etc`, `/var`, `/usr`, `/home`, and `/tmp` have specific roles.
  - `/etc` holds system-wide configuration files.
  - `/var` contains variable data like logs and caches.
  - `/usr` stores user-related programs and data.
  - `/home` is for user directories.
  - `/tmp` is a temporary space for transient files.

### Example: Checking Filesystem Usage

```bash
df -h            # Reports disk usage in human-readable format.
du -sh /var/log  # Checks the size of /var/log directory.
```

---

## 3. File Permissions, Ownership & Links

### File Permissions & Ownership

- **Permission Notation:**  
  Files have permissions for the owner, group, and others (e.g., rwxr-xr-x).
  - `r` = read (4)
  - `w` = write (2)
  - `x` = execute (1)

### Changing Permissions & Ownership

```bash
# Set permissions to rwxr-xr-x (755)
sudo chmod 755 /path/to/script.sh

# Change file owner and group
sudo chown user:group /path/to/file
```

### Hard vs. Soft (Symbolic) Links

- **Hard Links:**  
  Share the same inode; deleting the original does not affect the link.
- **Soft (Symbolic) Links:**  
  Reference the original file path; become broken if the target is removed.

```bash
# Creating a hard link:
ln file1.txt file1_hard.txt

# Creating a symbolic link:
ln -s file1.txt file1_soft.txt
```

---

## 4. Commonly Used Commands & Text Processing

### Basic Commands

- **Listing Files:**
  ```bash
  ls -la         # Lists all files with details.
  ```
- **Viewing File Contents:**
  ```bash
  cat /etc/passwd
  tail -n 10 /var/log/syslog
  head -n 5 /var/log/syslog
  less /var/log/syslog
  ```
- **Finding Files & Pattern Matching:**
  ```bash
  find /var/log -type f -name "*.log"
  grep "error" /var/log/syslog
  ```

### Advanced Text Processing

- **Using `sed` for replacements:**
  ```bash
  sed -i 's/oldtext/newtext/g' file.txt
  ```
- **Using `awk` for field extraction:**
  ```bash
  awk '{print $1, $3}' /etc/passwd
  ```
- **Using `cut` for specific columns:**
  ```bash
  cut -d: -f1 /etc/passwd
  ```

---

## 5. Process, Job & Resource Management

### Process Management

- **Listing Processes:**
  ```bash
  ps aux | grep apache
  top    # Interactive process monitor.
  htop   # A more user-friendly interactive process viewer.
  ```
- **Killing Processes:**
  ```bash
  kill -9 <PID>    # Forcefully terminate a process.
  ```

### Job Control

- **Background & Foreground Jobs:**
  ```bash
  ./long_running_script.sh &   # Run in background.
  jobs                         # List background jobs.
  fg %1                        # Bring job 1 to foreground.
  ```
- **Understanding Zombies:**  
  A zombie process is one that has finished execution but still shows in the process table because its exit status has not been read.

### Process Priorities

- **Using `nice` and `renice`:**
  ```bash
  nice -n 10 ./cpu_intensive_task.sh  # Start with lower priority.
  renice -n -5 -p <PID>                # Increase priority of an existing process.
  ```

---

## 6. Shell Scripting Essentials

### Scripting Best Practices

- **Shebang and Strict Modes:**
  ```bash
  #!/bin/bash
  set -euo pipefail   # Exit on error, undefined variable, or pipeline error.
  ```

### Example: Simple Backup Script

```bash
#!/bin/bash
set -euo pipefail
src="/path/to/source"
dest="/path/to/backup/$(date +%Y-%m-%d)"
mkdir -p "$dest"
cp -r "$src"/* "$dest"
echo "Backup completed successfully at $dest"
```

### Useful Concepts

- **Environment Variables:**
  ```bash
  echo $PATH
  export PATH="$PATH:/my/custom/bin"
  ```
- **Aliases and Functions:**  
  Quick shortcuts can be added in your shell configuration (e.g., `.bashrc` or `.zshrc`).
  ```bash
  alias ll='ls -la'
  function greet() {
      echo "Hello, $1!"
  }
  ```

---

## 7. Networking Fundamentals

### Basic Networking Commands

- **IP Configuration:**
  ```bash
  ip addr show
  ifconfig      # Older command; not always installed by default.
  ```
- **Testing Connectivity:**
  ```bash
  ping -c 4 google.com
  traceroute google.com   # Trace the network path.
  ```
- **Viewing Open Ports:**
  ```bash
  netstat -tulnp
  ss -tulnp
  ```

### DNS Tools

- **Domain Lookup:**
  ```bash
  dig example.com
  nslookup example.com
  ```

---

## 8. Package Management & Software Installation

### Package Managers

- **Debian/Ubuntu:**
  ```bash
  sudo apt-get update
  sudo apt-get install package-name
  ```
- **RedHat/CentOS:**
  ```bash
  sudo yum install package-name
  ```
- **Arch Linux:**
  ```bash
  sudo pacman -S package-name
  ```

### Viewing Package Details

- **For APT:**
  ```bash
  apt-cache show package-name
  ```
- **For RPM:**
  ```bash
  rpm -qi package-name
  ```

---

## 9. Job Scheduling: Cron vs. systemd Timers

### Cron Jobs

- **Editing Crontab:**
  ```bash
  crontab -e   # Open your personal cron file.
  ```
- **Example Cron Entry:**
  ```cron
  # Run backup script every day at 2 AM
  0 2 * * * /usr/local/bin/backup.sh
  ```

### systemd Timers

- **Creating a Timer Unit:**
  *Service Unit (`/etc/systemd/system/myjob.service`):*
  ```ini
  [Unit]
  Description=Run My Backup Job

  [Service]
  ExecStart=/usr/local/bin/backup.sh
  ```
  *Timer Unit (`/etc/systemd/system/myjob.timer`):*
  ```ini
  [Unit]
  Description=Timer for My Backup Job

  [Timer]
  OnCalendar=*-*-* 02:00:00
  Persistent=true

  [Install]
  WantedBy=timers.target
  ```
- **Enabling the Timer:**
  ```bash
  sudo systemctl daemon-reload
  sudo systemctl enable --now myjob.timer
  ```

---

## 10. Basic Troubleshooting & Log Management

### Log Files

- **System Logs:**  
  Most logs are found in `/var/log`. For example, `/var/log/syslog` or `/var/log/messages` (depending on distro).
- **Using `journalctl` (for systemd):**
  ```bash
  journalctl -u sshd.service --since "1 hour ago"
  ```

### Troubleshooting Commands

- **dmesg:** Displays kernel messages.
  ```bash
  dmesg | tail -n 20
  ```
- **strace:** Trace system calls for a process.
  ```bash
  sudo strace -p <PID> -o trace.log
  ```

---

## 11. Interview Questions & Answers

### Q1: What is the difference between soft and hard links?
- **Answer:**  
  A hard link points directly to the file’s inode and remains valid as long as one link exists. A soft link (symbolic link) points to the file’s pathname and becomes broken if the original file is removed.
- **Example:**
  ```bash
  ln file.txt hardlink.txt
  ln -s file.txt softlink.txt
  ```

### Q2: How does the `set -euo pipefail` command improve your shell scripts?
- **Answer:**  
  - `-e`: Exits the script immediately if any command fails.
  - `-u`: Treats unset variables as errors.
  - `-o pipefail`: Makes the script fail if any command in a pipeline fails.
- **Example:**
  ```bash
  #!/bin/bash
  set -euo pipefail
  echo "This script exits on any error."
  ```

### Q3: How do you find files that contain a specific string and what command would you use?
- **Answer:**  
  You can combine `find` and `grep` to search for files with a certain pattern.
- **Example:**
  ```bash
  find /etc -type f -name "*.conf" -exec grep -H "Listen" {} \;
  ```

### Q4: What is the purpose of the `/proc` filesystem?
- **Answer:**  
  The `/proc` filesystem is a pseudo-filesystem that provides an interface to kernel data structures. It’s commonly used to obtain system and process information.
- **Example:**
  ```bash
  cat /proc/cpuinfo  # Displays CPU information.
  ```

### Q5: Describe how you would schedule a recurring job without using cron?
- **Answer:**  
  You can use systemd timers as an alternative to cron. They provide better logging, dependency tracking, and integration with system events.
- **Example:** (Refer to section on Job Scheduling above.)

### Q6: What is `umask` and how does it affect file creation?
- **Answer:**  
  `umask` sets the default permission bits for newly created files and directories. For example, a umask of 022 means new files have permissions 644 (rw-r--r--) and directories have 755 (rwxr-xr-x). You can view the current umask with:
  ```bash
  umask
  ```

### Q7: How do you display the last part of a file and continually monitor it?
- **Answer:**  
  Use the `tail` command with the `-f` option:
  ```bash
  tail -f /var/log/syslog
  ```

---

## 12. Conclusion
