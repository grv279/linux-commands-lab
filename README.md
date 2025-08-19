# 🐧 Practical Everyday Linux Commands

A handy collection of everyday Linux commands for system administrators, developers, and power users. 
Categorized by common operations like user management, filesystem handling, networking, security, and more.

---

## 📌 Table of Contents

- [🐧 Practical Everyday Linux Commands](#-practical-everyday-linux-commands)
  - [📌 Table of Contents](#-table-of-contents)
  - [📖 Introduction](#-introduction)
  - [👥 User and Group Management](#-user-and-group-management)
    - [`adduser`, `deluser`](#adduser-deluser)
    - [`addgroup`, `delgroup`](#addgroup-delgroup)
    - [`password`](#password)
  - [📂 Filesystem Operations](#-filesystem-operations)
    - [🧭 `find` Command](#-find-command)
  - [🧰 Text Processing and Stream Editing](#-text-processing-and-stream-editing)
    - [📄🔍 `grep` command (with `-E` and Extended Regex)](#-grep-command-with--e-and-extended-regex)
    - [🔍 `grep -E`: Extended Regular Expressions (ERE)](#-grep--e-extended-regular-expressions-ere)
    - [📄 Quick Extended Regex Guide](#-quick-extended-regex-guide)
    - [`sed`](#sed)
  - [🌐 Networking](#-networking)
  - [📊 Process and System Monitoring](#-process-and-system-monitoring)
  - [🔒 Security and Permissions](#-security-and-permissions)
  - [📦 Package Management](#-package-management)
    - [🛠️ Debian/Ubuntu (APT)](#️-debianubuntu-apt)
    - [🛠️ Red Hat/CentOS (DNF/YUM)](#️-red-hatcentos-dnfyum)
  - [📦 Compression and Archiving](#-compression-and-archiving)
    - [🗄️ `tar`](#️-tar)
    - [📦 `zip`](#-zip)
  - [💡 Quick Rule](#-quick-rule)
  - [💽 Disk and Partition Management](#-disk-and-partition-management)

---

## 📖 Introduction

A quick-reference guide to essential Linux commands with practical examples. Ideal for daily use, troubleshooting, 
or reference.

---

## 👥 User and Group Management

### `adduser`, `deluser`

- Add a new user:
  ```bash
  sudo adduser username
  ```
- Delete a user:
  ```bash
  sudo deluser username
  ```

### `addgroup`, `delgroup`

- Add a new group:
  ```bash
  sudo addgroup groupname
  ```
- Remove a group:
  ```bash
  sudo delgroup groupname
  ```
- Add user to group:
  ```bash
  sudo usermod -aG groupname username
  ```
  > Removing a group may cause permission issues. You can check membership with:
  ```bash
  getent group groupname
  ```
  - View current user groups:
  ```bash
  groups
  ```

### `password`

- Change user password:
  ```bash
  passwd username
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

## 🧰 Text Processing and Stream Editing

### 📄🔍 `grep` command (with `-E` and Extended Regex)

#### Basics

```bash
grep "needle" file.txt          # print matching lines
grep -n "needle" file.txt       # show line numbers
grep -H "needle" file1 file2    # always show filename
grep -i "needle" file.txt       # case-insensitive
grep -v "needle" file.txt       # invert match (non-matching lines)
grep -w "word" file.txt         # match whole word
grep -x "exact line" file.txt   # match whole line
grep -c "needle" file.txt       # count matches per file
grep -l "needle" *.log          # list files that contain it
grep -L "needle" *.log          # list files that DO NOT contain it
grep -o "pat" file.txt          # only print the matched part
grep -m 3 "pat" file.txt        # stop after 3 matches
grep -q "pat" file.txt && echo "found"   # quiet (exit code only)
```

**Exit codes:** `0`=match found, `1`=no match, `2`=error.

---

#### Context around matches

```bash
grep -nC 2 "error" app.log      # 2 lines of context (before & after)
grep -nA 3 "start" app.log      # 3 lines After
grep -nB 1 "fail" app.log       # 1 line Before
```

---

#### Recursive search

```bash
grep -R "TODO" .                           # recurse (follows symlinks)
grep -r --exclude="*.min.js" "TODO" .      # exclude by glob
grep -r --exclude-dir=".git" "TODO" .      # exclude dir
grep -r --include="*.{c,h}" "malloc" src/  # include by glob
```

---

#### Multiple patterns

```bash
grep -E "error|warning|fail" app.log       # any of the alternatives
grep -e "error" -e "warning" app.log       # OR with multiple -e
grep -f patterns.txt app.log               # patterns from file (1/line)
```

---

#### Binary / Null-safety

```bash
grep --binary-files=without-match "pat" *  # skip binary noise
find . -type f -print0 | xargs -0 grep -n "pat"   # null-safe paths
grep -Z -l "pat" | xargs -0 -I{} echo {}         # NUL after filename
```

---

#### Faster fixed-string search

```bash
grep -F "literally this text" file.txt     # no regex, fastest
```

---

###  🔍 `grep -E`: Extended Regular Expressions (ERE)

`-E` enables **extended** regex (like `egrep`). You get `()`, `|`, `+`, `?`, and `{m,n}` without backslashes.

```bash
grep -E "cat|dog" pets.txt                 # alternation
grep -E "colou?r" words.txt                # optional u: color/colour
grep -E "go{2,4}gle" words.txt             # 2 to 4 o's
grep -E "^(INFO|WARN|ERROR):" app.log      # group + anchor
grep -E "^[A-Z][a-z]+$" names.txt          # simple “Name” shape
grep -E "^[0-9]{3}-[0-9]{2}-[0-9]{4}$"     # 123-45-6789 style
grep -E "[[:digit:]]{4}-[[:digit:]]{2}-[[:digit:]]{2}" dates.txt
```

> Tip: `-E` **does not** support lookarounds or `\b` word boundaries. Use `-w`, `\<`/`\>` (GNU extension), or switch to PCRE with `-P` if you need Perl features.

---

###  📄 Quick Extended Regex Guide

**Anchors**
- `^` start of line
- `$` end of line

**Character classes**
- `[abc]` any of a, b, c
- `[^abc]` not a, b, or c
- Ranges: `[A-Z]`, `[0-9]`
- POSIX classes (locale-aware):  
  `[:alnum:] [:alpha:] [:digit:] [:xdigit:] [:space:] [:punct:] [:lower:] [:upper:]`
  Use inside brackets: `[[:digit:]]`, `[[:space:]]+`

**Quantifiers**
- `?` 0 or 1
- `*` 0 or more
- `+` 1 or more
- `{m}`, `{m,}`, `{m,n}` exact/at-least/range

**Groups & Alternation**
- `( … )` group
- `|` alternation, e.g., `(cat|dog)s?`

**Escapes**
- `\.` literal dot
- `\+ \? \{` usually not needed in ERE (they’re operators)
- For a literal `-` in `[]`, put it first/last: `[-_]`

**Backreferences?**  
POSIX ERE doesn’t include backreferences; GNU `grep -E` won’t do `\1`.  
If you need backreferences, either:
- Use **BRE**: `grep "\(..*\)\1" file` (note the escaped `\(` `\)`), or
- Use **PCRE**: `grep -P "(..*)\1" file`.

---

### `sed`



- awk

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

### 🛠️ Debian/Ubuntu (APT)

  ```bash
  sudo apt update                            # Update package list

  sudo apt install packagename               # Install a package

  sudo apt list --installed packagename      # Check installed package version

  sudo apt install nginx=1.18.0-0ubuntu1     # Install a specific package version

  sudo apt remove packagename                # Remove a package
  ```

### 🛠️ Red Hat/CentOS (DNF/YUM)

```bash
sudo dnf check-update                        # Update package list

sudo dnf install packagename                 # Install a package

rpm -q packagename                           # Check installed package version

sudo dnf install nginx-1.20.1                # Install a specific package version

sudo dnf remove packagename                  # Remove a package
```

---

## 📦 Compression and Archiving

### 🗄️ `tar`

  ```bash
  tar -czvf archive.tar.gz folder/    # Create a tar.gz archive

  tar -xzvf archive.tar.gz            # Extract a tar.gz file
  ```

### 📦 `zip`

  ```bash
  zip archive.zip file1 file2         # Zip and unzip files

  unzip archive.zip                   # unzip to current directory
  ```

## 💡 Quick Rule

- **Use `tar`** for backups, Linux deployments, and when you need to preserve Unix permissions or handle lots of small files efficiently.  
- **Use `zip`** for cross-platform sharing, especially with Windows users, where `.zip` is universally supported.


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
