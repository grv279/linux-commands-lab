# 🐧 Practical Everyday Linux Commands

A handy collection of everyday Linux commands for system administrators, developers, and power users. 
Categorized by common operations like user management, filesystem handling, networking, security, and more.

---

## 📌 Table of Contents

- [🐧 Practical Everyday Linux Commands](#-practical-everyday-linux-commands)
  - [📌 Table of Contents](#-table-of-contents)
  - [📖 Introduction](#-introduction)
  - [👥 User and Group Management](#-user-and-group-management)
  - [📂 Filesystem Operations](#-filesystem-operations)
    - [🧭 `find` Command](#-find-command)
  - [🧰 Text Processing and Stream Editing](#-text-processing-and-stream-editing)
  - [🌐 Networking](#-networking)
  - [📊 Process and System Monitoring](#-process-and-system-monitoring)
  - [🔒 Security and Permissions](#-security-and-permissions)
  - [📦 Package Management](#-package-management)
    - [Debian/Ubuntu (APT)](#debianubuntu-apt)
    - [Red Hat/CentOS (DNF/YUM)](#red-hatcentos-dnfyum)
  - [📦 Compression and Archiving](#-compression-and-archiving)
  - [💽 Disk and Partition Management](#-disk-and-partition-management)
  - [🛠️ Others and Useful Shortcuts](#️-others-and-useful-shortcuts)
  - [📚 References](#-references)

---

## 📖 Introduction

A quick-reference guide to essential Linux commands with practical examples. Ideal for daily use, troubleshooting, 
or reference.

---

## 👥 User and Group Management

- Add a new user:
  ```bash
  sudo adduser username
  ```
- Delete a user:
  ```bash
  sudo deluser username
  ```
- Add a new group:
  ```bash
  sudo addgroup groupname
  ```
- Add user to group:
  ```bash
  sudo usermod -aG groupname username
  ```
- Change user password:
  ```bash
  passwd username
  ```
- View current user groups:
  ```bash
  groups
  ```

---

## 📂 Filesystem Operations

---


### 🧭 `find` Command

`find` is a **powerful recursive search** tool to filter files by type, name, time, size, permissions, etc., and take actions on them.

---

#### 🧩 Basic Syntax

```bash
find [start_path] [tests] [actions]
```

---

#### 🧪 Tests

| Test               | Description                           |
|--------------------|----------------------------------------|
| `-type f`          | Match regular files                   |
| `-type d`          | Match directories                     |
| `-name "*.txt"`    | Match files named `*.txt`             |
| `-iname "*.jpg"`   | Match `.jpg` (case-insensitive)       |
| `! -type d`        | Match anything *not* a directory      |
| `-size +10M`       | Files larger than 10 MB               |
| `-mtime -1`        | Modified in the last 1 day            |
| `-user root`       | Owned by root                         |
| `-perm 755`        | Files with exact permission 755       |
| `-and`, `-or`, `!` | Logical operators for combining tests |

---

#### ⚙️ Actions

| Action            | Description                             |
|-------------------|-----------------------------------------|
| `-print`          | Print the filename (default if omitted) |
| `-ls`             | List like `ls -l`                       |
| `-delete`         | Delete the file                         |
| `-exec CMD {} \;` | Execute a command on each file         |
| `-exec CMD {} +`  | Efficient batch execution               |

---

#### 💡 Example

```bash
find . -type f \( -iname "*.jpg" -or -iname "*.png" \) -exec ls -lh {} \;
```

---

#### ⏳ Time-Based Search

| Option        | Meaning                                |
|---------------|----------------------------------------|
| `-mtime +N`   | Modified more than N *days* ago        |
| `-mtime -N`   | Modified less than N *days* ago        |
| `-mtime N`    | Modified exactly N days ago            |
| `-mmin +/-N`  | Like `-mtime`, but in **minutes**      |

```bash
find . -type f -mtime -7
find /var/log -type f -mmin -60
```

---

#### 📦 Size-Based Search

| Option        | Meaning                     |
|---------------|-----------------------------|
| `-size +N`    | Size greater than N units   |
| `-size -N`    | Size less than N units      |
| `-size N`     | Size exactly N units        |

**Units:**  
- `c` = bytes  
- `k` = KB  
- `M` = MB  
- `G` = GB  

```bash
find . -type f -size +10M
find . -type f -size -100k
find . -type f -iname "*.log" -size +5M
```

---

#### ⚙️ Using `-exec` to Run Commands

```bash
find [path] [conditions] -exec [command] {} \;
```

- `{}` is replaced with the found file  
- `\;` ends the command  
- `\+` allows batching for efficiency

Examples:

```bash
# Delete files older than 30 days
find /tmp -type f -mtime +30 -exec rm {} \;

# Make `.sh` files executable
find . -type f -iname "*.sh" -exec chmod +x {} \;

# Count lines in text files
find . -type f -iname "*.txt" -exec wc -l {} \;

# Batch compress log files
find . -type f -iname "*.log" -exec gzip {} \+
```

---

#### 🔒 Safe Handling: `-print0` and `xargs -0`

Safely handle filenames with spaces/special characters:

```bash
find . -type f -iname "*.mp3" -print0 | xargs -0 mv -t ./music/
```

---

#### 🔄 Combined Examples

```bash
# Compress .log files >5MB modified in last 2 days
find . -type f -iname "*.log" -mtime -2 -size +5M -print0 | xargs -0 gzip

# Delete .tmp files older than 10 days
find . -type f -iname "*.tmp" -mtime +10 -exec rm -v {} \;
```

---

#### 📦 Using `du` for Human-Readable Sizes

```bash
find /path/to/directory -type f -exec du -h {} +
```

**Output:**
```
4.0K	./user_script_openssl
72K	./company_final_invoice.pdf
68M	./master-pdf-editor-5.9.70-qt5.9.x86_64.deb
```

Display in MB:

```bash
find /path/to/directory -type f -exec du -m {} +
```

Directory summary (non-recursive):

```bash
find /path/to/directory -maxdepth 1 -type d -exec du --max-depth=1 -h {} +
```

---

#### 📄 Using `ls` for File Info

```bash
find /path/to/directory -type f -exec ls -lh {} +
```

Display in KB:

```bash
find /path/to/directory -type f -exec ls -l --block-size=KB {} +
```

Sort by size:

```bash
find /path/to/directory -type f -print0 | xargs -0 ls -lS --block-size=KB
```

Limit to current directory:

```bash
find /path/to/directory -maxdepth 1 -type f -print0 | xargs -0 ls -lS --block-size=KB
```

---

#### 🔍 Filter with `grep` and `sort`

Show files in MB:

```bash
find /path/to/directory -maxdepth 1 -type f -exec du -h {} + | grep -E '\b[0-9]+M\b'
```

Filter by name:

```bash
find /path/to/directory -type f -exec du -h {} + | grep 'specific_keyword'
```

Sort ascending:

```bash
find /path/to/directory -maxdepth 1 -type f -exec du -h {} + | sort -h -k1
```

Sort descending:

```bash
find /path/to/directory -maxdepth 1 -type f -exec du -h {} + | sort -rh -k1
```

---

#### 📏 Filter by Size with `-size`

Files > 10 MB:

```bash
find /path/to/directory -maxdepth 1 -type f -size +10M -exec du -h {} +
```

Files > 10 KB, sorted:

```bash
find /path/to/directory -maxdepth 1 -type f -size +10k -exec du -h {} + | sort -h -k1
```

---








---

## 🧰 Text Processing and Stream Editing

- grep

- awk

- sed

---

## 🌐 Networking

- Show IP address:
  ```bash
  ip addr show
  ```
- Test connectivity:
  ```bash
  ping google.com
  ```
- Display network routes:
  ```bash
  ip route
  ```
- Download files:
  ```bash
  wget https://example.com/file
  curl -O https://example.com/file
  ```
- Check open ports:
  ```bash
  ss -tuln
  ```
- View active connections:
  ```bash
  netstat -tulnp
  ```

---

## 📊 Process and System Monitoring

- View running processes:
  ```bash
  ps aux
  top
  htop
  ```
- Kill a process by PID:
  ```bash
  kill -9 PID
  ```
- View memory and CPU usage:
  ```bash
  free -h
  vmstat
  uptime
  ```

---

## 🔒 Security and Permissions

- Change file ownership:
  ```bash
  chown user:group filename
  ```
- Modify permissions:
  ```bash
  chmod 755 script.sh
  ```
- View file permissions:
  ```bash
  ls -l filename
  ```
- Use `sudo` for admin tasks:
  ```bash
  sudo command
  ```
- Check user login history:
  ```bash
  last
  who
  ```

---

## 📦 Package Management

### Debian/Ubuntu (APT)
- Update package list:
  ```bash
  sudo apt update
  ```
- Install a package:
  ```bash
  sudo apt install packagename
  ```
- Remove a package:
  ```bash
  sudo apt remove packagename
  ```

### Red Hat/CentOS (DNF/YUM)
- Install a package:
  ```bash
  sudo dnf install packagename
  ```

---

## 📦 Compression and Archiving

- Create a tar.gz archive:
  ```bash
  tar -czvf archive.tar.gz folder/
  ```
- Extract a tar.gz file:
  ```bash
  tar -xzvf archive.tar.gz
  ```
- Zip and unzip files:
  ```bash
  zip archive.zip file1 file2
  unzip archive.zip
  ```

---

## 💽 Disk and Partition Management

- List block devices:
  ```bash
  lsblk
  ```
- View partition layout:
  ```bash
  fdisk -l
  ```
- Format a device:
  ```bash
  sudo mkfs.ext4 /dev/sdX1
  ```

---

## 🛠️ Others and Useful Shortcuts

- View command history:
  ```bash
  history
  ```
- Run command as root:
  ```bash
  sudo command
  ```
- Redirect output to a file:
  ```bash
  ls > file.txt
  ```
- Append output to a file:
  ```bash
  echo "log" >> logfile.txt
  ```
- Schedule a task (cron):
  ```bash
  crontab -e
  ```

---

## 📚 References

- [TLDP Bash Guide](https://tldp.org/LDP/Bash-Beginners-Guide/html/)
- `man` pages: Use `man command` to view manual pages
- [LinuxCommand.org](http://linuxcommand.org/)
- [Explainshell](https://explainshell.com/)

---