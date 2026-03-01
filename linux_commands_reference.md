# Linux Commands Reference Manual
> A practical field guide for VPS administration — no `man` page diving required.

---

## Alphabetic Index

| Command | Section |
|---------|---------|
| [`cat`](#cat) | [File Viewing](#3-file-viewing--content) |
| [`cd`](#cd) | [Navigation](#1-navigation) |
| [`chmod`](#chmod) | [Permissions](#6-permissions--ownership) |
| [`chown`](#chown) | [Permissions](#6-permissions--ownership) |
| [`cp`](#cp) | [File Manipulation](#2-file-manipulation) |
| [`curl`](#curl) | [Network](#8-network-tools) |
| [`df`](#df) | [Disk & System Info](#7-disk--system-info) |
| [`diff`](#diff) | [File Viewing](#3-file-viewing--content) |
| [`du`](#du) | [Disk & System Info](#7-disk--system-info) |
| [`echo`](#echo) | [Text & Output](#4-text--output) |
| [`find`](#find) | [Search](#5-search--filtering) |
| [`free`](#free) | [Disk & System Info](#7-disk--system-info) |
| [`grep`](#grep) | [Search](#5-search--filtering) |
| [`head`](#head) | [File Viewing](#3-file-viewing--content) |
| [`history`](#history) | [Shell & Session](#10-shell--session) |
| [`htop`](#htop) | [Process Management](#9-process-management) |
| [`ip`](#ip) | [Network](#8-network-tools) |
| [`journalctl`](#journalctl) | [System Services](#11-system-services-systemd) |
| [`kill`](#kill) | [Process Management](#9-process-management) |
| [`less`](#less) | [File Viewing](#3-file-viewing--content) |
| [`ln`](#ln) | [File Manipulation](#2-file-manipulation) |
| [`ls`](#ls) | [Navigation](#1-navigation) |
| [`man`](#man) | [Shell & Session](#10-shell--session) |
| [`mkdir`](#mkdir) | [File Manipulation](#2-file-manipulation) |
| [`mv`](#mv) | [File Manipulation](#2-file-manipulation) |
| [`nano`](#nano) | [Text Editors](#12-text-editors) |
| [`netstat`](#netstat) | [Network](#8-network-tools) |
| [`ping`](#ping) | [Network](#8-network-tools) |
| [`ps`](#ps) | [Process Management](#9-process-management) |
| [`pwd`](#pwd) | [Navigation](#1-navigation) |
| [`rm`](#rm) | [File Manipulation](#2-file-manipulation) |
| [`rsync`](#rsync) | [File Manipulation](#2-file-manipulation) |
| [`sed`](#sed) | [Text & Output](#4-text--output) |
| [`sort`](#sort) | [Text & Output](#4-text--output) |
| [`ss`](#ss) | [Network](#8-network-tools) |
| [`ssh`](#ssh) | [Network](#8-network-tools) |
| [`sudo`](#sudo) | [Permissions](#6-permissions--ownership) |
| [`systemctl`](#systemctl) | [System Services](#11-system-services-systemd) |
| [`tail`](#tail) | [File Viewing](#3-file-viewing--content) |
| [`tar`](#tar) | [File Manipulation](#2-file-manipulation) |
| [`top`](#top) | [Process Management](#9-process-management) |
| [`touch`](#touch) | [File Manipulation](#2-file-manipulation) |
| [`uname`](#uname) | [Disk & System Info](#7-disk--system-info) |
| [`uniq`](#uniq) | [Text & Output](#4-text--output) |
| [`uptime`](#uptime) | [Disk & System Info](#7-disk--system-info) |
| [`vim`](#vim) | [Text Editors](#12-text-editors) |
| [`wc`](#wc) | [Text & Output](#4-text--output) |
| [`wget`](#wget) | [Network](#8-network-tools) |
| [`which`](#which) | [Shell & Session](#10-shell--session) |
| [`whoami`](#whoami) | [Shell & Session](#10-shell--session) |
| [`xargs`](#xargs) | [Text & Output](#4-text--output) |

---

## How to Read This Manual

Each command entry follows this structure:
```
COMMAND — what it does in one line

Syntax:   command [flags] [arguments]
Flags:    -x    what this flag does
Examples: concrete usage
Notes:    traps, warnings, tips
```

**Tier system:**
- 🔴 **Tier 1** — Use daily. Learn these first.
- 🟡 **Tier 2** — Use regularly. Learn after Tier 1.
- 🔵 **Tier 3** — Use occasionally. Reference when needed.

---

---

# 1. Navigation

*Moving around the filesystem. The filesystem is a tree; you always have a "current location" (working directory). Paths starting with `/` are absolute (from root). Paths without `/` at the start are relative (from where you are now).*

---

### `pwd`
🔴 **Tier 1** — **P**rint **W**orking **D**irectory. Shows your current location in the filesystem.

```
Syntax:   pwd
```

No important flags. Just run it.

**Examples:**
```bash
pwd
# Output: /home/alex/configs
# This tells you: you are in /home/alex/configs
```

---

### `ls`
🔴 **Tier 1** — **L**i**s**t directory contents.

```
Syntax:   ls [flags] [path]
```

| Flag | Meaning |
|------|---------|
| `-l` | Long format: shows permissions, owner, size, date |
| `-a` | Show **all** files, including hidden ones (files starting with `.`) |
| `-h` | Human-readable sizes (KB, MB, GB) — use with `-l` |
| `-t` | Sort by modification **time**, newest first |
| `-r` | **Reverse** sort order |
| `-R` | **Recursive**: list subdirectories too |

Flags can be combined: `-lah` = long format + all files + human-readable sizes.

**Examples:**
```bash
ls                    # list current directory
ls /etc               # list /etc directory
ls -l                 # detailed list (permissions, size, date)
ls -lah               # detailed + hidden + readable sizes (most useful combo)
ls -lah /etc/nginx    # inspect nginx config directory in detail
ls -lt                # sorted by time, most recent first
ls -ltr               # sorted by time, oldest first (reversed)
```

**Sample output of `ls -lah`:**
```
drwxr-xr-x  3 alex alex 4.0K Jan 15 10:22 .
drwxr-xr-x 12 alex alex 4.0K Jan 14 09:15 ..
-rw-r--r--  1 alex alex  220 Jan 14 09:15 .bash_logout
-rw-------  1 alex alex 1.2K Jan 15 10:20 .bash_history
drwxr-xr-x  2 alex alex 4.0K Jan 15 10:00 configs
# d = directory, - = file, rwx = read/write/execute permissions
```

---

### `cd`
🔴 **Tier 1** — **C**hange **D**irectory. Move to a different location.

```
Syntax:   cd [path]
```

No flags, but special path shortcuts:

| Shortcut | Meaning |
|----------|---------|
| `cd` | Go to your home directory (`/home/username`) |
| `cd ~` | Same as above (`~` always means home) |
| `cd ..` | Go one level **up** (parent directory) |
| `cd -` | Go to the **previous** directory (toggle back/forward) |
| `cd /` | Go to the **root** of the filesystem |

**Examples:**
```bash
cd /etc/nginx         # go to nginx config directory (absolute path)
cd configs            # go into 'configs' folder in current dir (relative)
cd ..                 # go up one level
cd ../..              # go up two levels
cd ~                  # go to home
cd -                  # go back to where you were last
```

---

---

# 2. File Manipulation

*Creating, copying, moving, deleting files and directories.*

---

### `touch`
🔴 **Tier 1** — Create an empty file, or update a file's timestamp.

```
Syntax:   touch [flags] filename
```

| Flag | Meaning |
|------|---------|
| `-m` | Only update modification time, don't create |

**Examples:**
```bash
touch config.txt          # create empty file named config.txt
touch file1 file2 file3   # create multiple files at once
```

---

### `mkdir`
🔴 **Tier 1** — **M**a**k**e **Dir**ectory.

```
Syntax:   mkdir [flags] dirname
```

| Flag | Meaning |
|------|---------|
| `-p` | Create **parent** directories as needed (no error if exists) |
| `-m` | Set permissions at creation time (e.g. `-m 755`) |

**Examples:**
```bash
mkdir configs                       # create folder 'configs'
mkdir -p /opt/myapp/logs/archive    # create full path, including all missing parents
mkdir -p projects/{frontend,backend,docs}  # create 3 subdirs at once (brace expansion)
```

---

### `cp`
🔴 **Tier 1** — **C**o**p**y files or directories.

```
Syntax:   cp [flags] source destination
```

| Flag | Meaning |
|------|---------|
| `-r` | **Recursive**: copy directories and their contents |
| `-i` | **Interactive**: ask before overwriting |
| `-v` | **Verbose**: print each file as it's copied |
| `-p` | **Preserve** timestamps, permissions, owner |
| `-a` | **Archive**: `-rp` + more — best for full backups |
| `-n` | Do **not** overwrite existing files |

**Examples:**
```bash
cp file.txt backup.txt              # copy file.txt → backup.txt (same dir)
cp file.txt /tmp/                   # copy file.txt into /tmp/ directory
cp -r configs/ configs_backup/     # copy entire directory recursively
cp -av /etc/nginx/ ~/nginx_backup/  # archive copy with verbose output
cp -i important.conf /etc/          # ask before overwriting
```

> ⚠️ If `destination` is an existing directory, file gets placed **inside** it. If it doesn't exist, file gets **renamed** to it.

---

### `mv`
🔴 **Tier 1** — **M**o**v**e or rename files/directories.

```
Syntax:   mv [flags] source destination
```

| Flag | Meaning |
|------|---------|
| `-i` | **Interactive**: ask before overwriting |
| `-v` | **Verbose**: show what's being moved |
| `-n` | Do **not** overwrite existing files |

**Examples:**
```bash
mv old_name.txt new_name.txt        # rename file
mv file.txt /tmp/                   # move to /tmp/
mv -v *.log /var/log/archive/       # move all .log files, show progress
mv -i config.bak /etc/nginx/        # ask before overwriting
```

> ℹ️ `mv` is instant for files on the same filesystem (just updates the path). Across filesystems, it copies + deletes.

---

### `rm`
🔴 **Tier 1** — **R**e**m**ove files or directories. **Permanent — no recycle bin.**

```
Syntax:   rm [flags] target
```

| Flag | Meaning |
|------|---------|
| `-r` | **Recursive**: delete directories and everything inside |
| `-f` | **Force**: no error if file doesn't exist, no prompts |
| `-i` | **Interactive**: confirm each deletion |
| `-v` | **Verbose**: show each deleted file |

**Examples:**
```bash
rm file.txt                   # delete a single file
rm file1.txt file2.txt        # delete multiple files
rm -r old_configs/            # delete directory and all contents
rm -rf /tmp/test_build/       # force-delete directory, no prompts
rm -i *.txt                   # delete all .txt files, confirming each one
```

> 🚨 **`rm -rf` is irreversible.** Always double-check the path. Never run `rm -rf /` or `rm -rf /*`.

---

### `ln`
🟡 **Tier 2** — Create **l**i**n**ks between files.

Two types:
- **Hard link**: second name pointing to same data on disk. Deleting original doesn't affect it.
- **Symbolic link (symlink)**: a shortcut pointer to another file/dir. Like a Windows shortcut.

```
Syntax:   ln [flags] target link_name
```

| Flag | Meaning |
|------|---------|
| `-s` | Create a **symbolic** (soft) link |
| `-f` | **Force**: overwrite existing link |
| `-v` | **Verbose** |

**Examples:**
```bash
ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled/mysite
# Creates symlink: sites-enabled/mysite → sites-available/mysite
# This is the standard nginx pattern for enabling sites

ln -s /opt/myapp/current /opt/myapp/active  # point 'active' to 'current'
ln file.txt hardlink.txt                    # hard link (no -s)
```

---

### `tar`
🟡 **Tier 2** — **T**ape **Ar**chive. Bundle files into one archive, optionally compressed.

```
Syntax:   tar [flags] archive_name.tar.gz [files/dirs]
```

| Flag | Meaning |
|------|---------|
| `-c` | **Create** a new archive |
| `-x` | **Extract** an archive |
| `-v` | **Verbose**: list files being processed |
| `-f` | Specifies the **filename** — always needed, file comes right after |
| `-z` | Filter through **gzip** (`.tar.gz` / `.tgz`) |
| `-j` | Filter through **bzip2** (`.tar.bz2`) — slower, better compression |
| `-J` | Filter through **xz** (`.tar.xz`) — best compression |
| `-t` | **List** contents without extracting |
| `-C` | Extract to a specific **directory** |

**Memory trick:** `c` = **c**reate, `x` = e**x**tract, `-f` always last before filename.

**Examples:**
```bash
# CREATE archives:
tar -czvf backup.tar.gz /etc/nginx/       # create gzip archive of nginx dir
tar -czvf logs.tar.gz *.log               # archive all .log files
tar -cjvf backup.tar.bz2 /var/www/       # bzip2 (better compression)

# EXTRACT archives:
tar -xzvf backup.tar.gz                   # extract into current directory
tar -xzvf backup.tar.gz -C /tmp/restore/ # extract into specific folder

# INSPECT without extracting:
tar -tzvf backup.tar.gz                   # list contents

# Quick reference pattern: c/x + z/j/J + v + f + filename
```

---

### `rsync`
🟡 **Tier 2** — **R**emote **sync**. Efficiently copy/sync files, locally or over SSH.

```
Syntax:   rsync [flags] source destination
```

| Flag | Meaning |
|------|---------|
| `-a` | **Archive** mode: recursive + preserves permissions, timestamps, symlinks, owner |
| `-v` | **Verbose** |
| `-z` | **Compress** data during transfer |
| `-P` | Show **progress** + keep partial files (= `--progress --partial`) |
| `-n` | **Dry run**: show what would happen, don't actually do it |
| `-e ssh` | Use **ssh** as transport |
| `--delete` | Delete files in destination that don't exist in source (mirror mode) |
| `--exclude` | Exclude files matching a pattern |

**Examples:**
```bash
# Local sync:
rsync -av source/ destination/        # sync two local directories
rsync -avn source/ destination/       # dry run first — always wise!

# Remote (over SSH):
rsync -avzP /var/www/ user@server:/var/www/   # upload local to remote
rsync -avzP user@server:/backups/ ~/backups/  # download from remote

# Mirror (exact copy, delete extras):
rsync -avz --delete source/ destination/

# Exclude patterns:
rsync -av --exclude='*.log' --exclude='.git/' source/ dest/
```

> ℹ️ Note the trailing slash on `source/` — `source/` copies **contents**, `source` copies the directory itself.

---

---

# 3. File Viewing & Content

*Reading file contents without editing them.*

---

### `cat`
🔴 **Tier 1** — Con**cat**enate. Print file contents to terminal.

```
Syntax:   cat [flags] filename
```

| Flag | Meaning |
|------|---------|
| `-n` | Show **line numbers** |
| `-A` | Show all characters including hidden (tabs, line endings) |

**Examples:**
```bash
cat file.txt                  # print entire file
cat -n /etc/nginx/nginx.conf  # print with line numbers
cat file1.txt file2.txt       # concatenate (print one after another)
cat file1.txt >> file2.txt    # append file1 contents to file2
```

> ℹ️ For large files, use `less` instead — `cat` will flood your terminal.

---

### `less`
🔴 **Tier 1** — View file content page by page. The go-to for large files.

```
Syntax:   less [flags] filename
```

| Navigation Key | Action |
|----------------|--------|
| `Space` / `f` | Next page |
| `b` | Previous page |
| `↑` / `↓` | Line by line |
| `g` | Go to **beginning** |
| `G` | Go to **end** |
| `/pattern` | **Search** forward (press `n` for next match, `N` for previous) |
| `?pattern` | Search **backward** |
| `q` | **Quit** |

| Flag | Meaning |
|------|---------|
| `-N` | Show line **numbers** |
| `-S` | Don't wrap long lines (scroll horizontally) |
| `+F` | Follow mode (like `tail -f`) |

**Examples:**
```bash
less /var/log/syslog          # browse system log
less -N /etc/nginx/nginx.conf # with line numbers
less +G /var/log/auth.log     # open at the END of file
```

---

### `head`
🔴 **Tier 1** — Show the beginning of a file.

```
Syntax:   head [flags] filename
```

| Flag | Meaning |
|------|---------|
| `-n N` | Show first **N** lines (default: 10) |
| `-c N` | Show first **N bytes** |

**Examples:**
```bash
head file.txt              # first 10 lines
head -n 20 file.txt        # first 20 lines
head -n 1 /etc/os-release  # just the first line
```

---

### `tail`
🔴 **Tier 1** — Show the end of a file. Essential for log watching.

```
Syntax:   tail [flags] filename
```

| Flag | Meaning |
|------|---------|
| `-n N` | Show last **N** lines (default: 10) |
| `-f` | **Follow**: keep watching and print new lines as they're added |
| `-F` | Follow and retry if file is rotated/recreated |

**Examples:**
```bash
tail file.txt               # last 10 lines
tail -n 50 /var/log/syslog  # last 50 lines
tail -f /var/log/nginx/access.log   # live watch nginx access log
tail -F /var/log/app.log    # follow even if log rotates
```

> ✅ `tail -f` is one of the most-used commands for monitoring servers in real time.

---

### `diff`
🟡 **Tier 2** — Show **diff**erences between two files.

```
Syntax:   diff [flags] file1 file2
```

| Flag | Meaning |
|------|---------|
| `-u` | **Unified** format (standard, shows context lines) |
| `-y` | Side-by-side comparison |
| `-r` | **Recursive** (compare directories) |
| `--color` | Colorized output |

**Examples:**
```bash
diff config.old config.new           # basic diff
diff -u config.old config.new        # unified (standard patch format)
diff -u --color config.old config.new
diff -r dir1/ dir2/                  # compare two directories
```

Output meaning: `<` = file1 only, `>` = file2 only, `---/+++` in unified format.

---

---

# 4. Text & Output

*Processing, transforming, and counting text.*

---

### `echo`
🔴 **Tier 1** — Print text to terminal. Also used in scripts.

```
Syntax:   echo [flags] "text"
```

| Flag | Meaning |
|------|---------|
| `-n` | Do **not** add a newline at the end |
| `-e` | Interpret escape sequences (`\n` = newline, `\t` = tab) |

**Examples:**
```bash
echo "Hello, World"              # print text
echo "PATH is: $PATH"            # print variable value
echo -e "Line1\nLine2\nLine3"    # print with newlines
echo "text" > file.txt           # write text to file (overwrites)
echo "text" >> file.txt          # append text to file
echo $USER                       # print current username
```

> ℹ️ `>` redirects output to a file (overwrites). `>>` appends. These work with any command.

---

### `grep`
🔴 **Tier 1** — **G**lobal **R**egular **E**xpression **P**rint. Search for patterns in text.

```
Syntax:   grep [flags] "pattern" [file]
         command | grep "pattern"
```

| Flag | Meaning |
|------|---------|
| `-i` | Case-**insensitive** search |
| `-r` | **Recursive**: search all files in a directory |
| `-n` | Show **line numbers** |
| `-v` | **Invert**: show lines that do NOT match |
| `-l` | Only show **filenames** with matches (not the lines) |
| `-c` | **Count** matching lines per file |
| `-A N` | Show N lines **after** match |
| `-B N` | Show N lines **before** match |
| `-C N` | Show N lines of **context** (before + after) |
| `-E` | Use **extended** regex (or use `egrep`) |
| `--color` | Highlight matches (often on by default) |

**Examples:**
```bash
grep "error" /var/log/syslog             # find 'error' in syslog
grep -i "error" /var/log/syslog         # case-insensitive
grep -rn "listen 80" /etc/nginx/        # find in all nginx files, show line numbers
grep -v "^#" /etc/nginx/nginx.conf      # exclude comment lines (starting with #)
grep -C 3 "failed" /var/log/auth.log    # show 3 lines context around 'failed'
grep -E "error|warning" /var/log/app.log  # match 'error' OR 'warning'

# Piping (very common pattern):
systemctl status nginx | grep "Active"  # filter command output
cat /etc/passwd | grep -v "nologin"     # exclude nologin users
```

> ✅ `grep` is incredibly powerful piped after other commands. The pattern `command | grep something` is used constantly.

---

### `sed`
🟡 **Tier 2** — **S**tream **Ed**itor. Transform text on the fly.

```
Syntax:   sed [flags] 'command' [file]
```

Most common `sed` command: `s/old/new/` (substitute).

| Pattern | Meaning |
|---------|---------|
| `s/old/new/` | Replace first occurrence per line |
| `s/old/new/g` | Replace **all** occurrences (global) |
| `s/old/new/I` | Case-insensitive replace |
| `/pattern/d` | **Delete** lines matching pattern |
| `p` | **Print** matched lines |

| Flag | Meaning |
|------|---------|
| `-i` | Edit file **in-place** (modify the file directly) |
| `-i.bak` | Edit in-place but make a `.bak` backup first |
| `-n` | Suppress default output (use with `p`) |
| `-E` | Extended regex |

**Examples:**
```bash
sed 's/http/https/' file.txt                 # replace first 'http' per line, print to screen
sed 's/http/https/g' file.txt                # replace all occurrences
sed -i 's/old_domain/new_domain/g' config    # edit file in-place
sed -i.bak 's/port=80/port=8080/' config     # in-place with backup
sed '/^#/d' file.txt                         # delete lines starting with #
sed -n '10,20p' file.txt                     # print only lines 10 through 20
```

---

### `wc`
🟡 **Tier 2** — **W**ord **C**ount. Count lines, words, characters.

```
Syntax:   wc [flags] [file]
```

| Flag | Meaning |
|------|---------|
| `-l` | Count **lines** |
| `-w` | Count **words** |
| `-c` | Count **bytes/characters** |

**Examples:**
```bash
wc file.txt                   # shows: lines words chars filename
wc -l /etc/passwd             # count users (one per line)
wc -l *.log                   # count lines in each .log file
grep "error" app.log | wc -l  # count how many errors in log
```

---

### `sort`
🟡 **Tier 2** — Sort lines of text.

```
Syntax:   sort [flags] [file]
```

| Flag | Meaning |
|------|---------|
| `-r` | **Reverse** order |
| `-n` | **Numeric** sort (not lexicographic) |
| `-u` | **Unique**: remove duplicate lines |
| `-k N` | Sort by field/column **N** |
| `-t char` | Set field **delimiter** (default: whitespace) |
| `-h` | **Human** numeric sort (handles 1K, 2M, etc.) |

**Examples:**
```bash
sort file.txt                         # alphabetical sort
sort -r file.txt                      # reverse alphabetical
sort -n numbers.txt                   # numeric sort
sort -u file.txt                      # sort + remove duplicates
ls -lah | sort -k5 -h                 # sort by file size (column 5)
cat /etc/passwd | sort -t: -k3 -n    # sort passwd by UID (field 3, : delimiter)
```

---

### `uniq`
🟡 **Tier 2** — Filter or count repeated lines. **Must have sorted input** to work correctly.

```
Syntax:   uniq [flags] [file]
```

| Flag | Meaning |
|------|---------|
| `-c` | **Count** occurrences |
| `-d` | Only show **duplicate** lines |
| `-u` | Only show **unique** (non-duplicated) lines |

**Examples:**
```bash
sort file.txt | uniq              # remove duplicate lines
sort file.txt | uniq -c           # count occurrences of each line
sort file.txt | uniq -c | sort -rn  # most frequent lines first (classic pattern)

# Log analysis example:
cat access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head -20
# → most frequent IP addresses in nginx access log
```

---

### `xargs`
🔵 **Tier 3** — Build command arguments from standard input.

```
Syntax:   command | xargs [flags] command
```

| Flag | Meaning |
|------|---------|
| `-I {}` | Replace `{}` with each input item |
| `-n N` | Pass N items at a time |
| `-P N` | Run N processes in **parallel** |
| `-0` | Items separated by null byte (use with `find -print0`) |

**Examples:**
```bash
find . -name "*.log" | xargs rm        # delete all found .log files
find . -name "*.conf" | xargs grep "listen"   # grep in all found files
ls *.txt | xargs -I {} cp {} /backup/  # copy each file to /backup/
cat list_of_files.txt | xargs rm -f   # delete files listed in a text file
```

---

---

# 5. Search & Filtering

---

### `find`
🔴 **Tier 1** — Search for files/directories in a directory tree.

```
Syntax:   find [path] [options] [actions]
```

| Option | Meaning |
|--------|---------|
| `-name "pattern"` | Search by **filename** (case-sensitive) |
| `-iname "pattern"` | Search by filename (**case-insensitive**) |
| `-type f` | Only regular **files** |
| `-type d` | Only **directories** |
| `-type l` | Only **symlinks** |
| `-size +N[k/M/G]` | Files **larger** than N kilobytes/megabytes/gigabytes |
| `-size -N[k/M/G]` | Files **smaller** than N |
| `-mtime -N` | Modified in last N **days** |
| `-mtime +N` | Modified more than N days **ago** |
| `-user username` | Files owned by user |
| `-perm 644` | Files with exact permissions |
| `-empty` | Empty files or directories |
| `-exec cmd {} \;` | **Execute** command on each result |
| `-print0` | Null-separated output (safe for filenames with spaces) |

**Examples:**
```bash
find /etc -name "*.conf"              # find all .conf files in /etc
find . -name "*.log" -type f          # log files in current directory tree
find /var/log -mtime -1               # files modified in last 24 hours
find / -size +100M -type f            # files larger than 100MB
find . -name "*.bak" -exec rm {} \;  # delete all .bak files found
find /etc -name "*.conf" -exec grep -l "ssl" {} \;  # .conf files containing 'ssl'
find . -empty -type f                 # find empty files
```

---

---

# 6. Permissions & Ownership

*Linux permissions: every file has an owner (user), a group, and permissions for user/group/others.*

**Permission notation:** `rwxrwxrwx` = user(rwx) group(rwx) others(rwx)
- `r` = read (4), `w` = write (2), `x` = execute (1)
- `755` = user:rwx (7), group:r-x (5), others:r-x (5)
- `644` = user:rw- (6), group:r-- (4), others:r-- (4)

---

### `sudo`
🔴 **Tier 1** — **S**uper**u**ser **do**. Execute a command with root privileges.

```
Syntax:   sudo command
```

| Option | Meaning |
|--------|---------|
| `sudo -i` | Open a root **shell** (interactive login) |
| `sudo -s` | Open a root shell (non-login) |
| `sudo -u user cmd` | Run command as a different **user** |
| `sudo !!` | Rerun the **last command** with sudo |

**Examples:**
```bash
sudo apt update                    # run apt as root
sudo nano /etc/nginx/nginx.conf    # edit system config file
sudo -i                            # become root (use with care)
sudo systemctl restart nginx       # restart system service
sudo -u www-data php artisan       # run as web server user
```

> ⚠️ Use `sudo` for specific commands rather than `sudo -i` (root shell) to minimize risk.

---

### `chmod`
🔴 **Tier 1** — **Ch**ange file **mod**e (permissions).

```
Syntax:   chmod [flags] permissions file
```

Two ways to specify permissions:

**Numeric (octal):**
| Permission | Value |
|-----------|-------|
| `rwx` | 7 |
| `rw-` | 6 |
| `r-x` | 5 |
| `r--` | 4 |
| `---` | 0 |

Common combinations: `755` (dirs/executables), `644` (regular files), `600` (private keys), `777` (all access — avoid)

**Symbolic:**
- `u` = user/owner, `g` = group, `o` = others, `a` = all
- `+` = add, `-` = remove, `=` = set exactly

| Flag | Meaning |
|------|---------|
| `-R` | **Recursive**: apply to all files in directory |
| `-v` | **Verbose** |

**Examples:**
```bash
chmod 755 script.sh               # owner: rwx, group+others: r-x
chmod 644 config.txt              # owner: rw-, others: r--
chmod 600 ~/.ssh/id_rsa           # SSH private key (owner read/write only)
chmod +x deploy.sh                # add execute permission for all
chmod u+x,g-w script.sh          # add execute for owner, remove write for group
chmod -R 755 /var/www/html/       # set 755 recursively on web root
```

---

### `chown`
🟡 **Tier 2** — **Ch**ange **own**ership of files.

```
Syntax:   chown [flags] user[:group] file
```

| Flag | Meaning |
|------|---------|
| `-R` | **Recursive** |
| `-v` | **Verbose** |

**Examples:**
```bash
chown alex file.txt                 # change owner to 'alex'
chown alex:alex file.txt            # change owner and group to 'alex'
chown www-data:www-data /var/www/   # nginx/apache ownership
chown -R alex:alex /opt/myapp/      # recursively change ownership
chown root:root /etc/passwd         # root should own this
```

---

---

# 7. Disk & System Info

---

### `df`
🔴 **Tier 1** — **D**isk **f**ree. Show filesystem disk space usage.

```
Syntax:   df [flags] [path]
```

| Flag | Meaning |
|------|---------|
| `-h` | **Human**-readable sizes (KB/MB/GB) |
| `-T` | Show filesystem **type** |
| `-i` | Show **inode** usage instead of block usage |

**Examples:**
```bash
df -h              # disk usage for all mounted filesystems
df -hT             # with filesystem type
df -h /var         # disk usage for the filesystem containing /var
```

---

### `du`
🔴 **Tier 1** — **D**isk **u**sage. Show size of files and directories.

```
Syntax:   du [flags] [path]
```

| Flag | Meaning |
|------|---------|
| `-h` | **Human**-readable |
| `-s` | **Summary**: show only total for each argument |
| `-a` | Show **all** files, not just directories |
| `--max-depth=N` | Limit depth of recursion |
| `-c` | Show grand total |

**Examples:**
```bash
du -sh /var/log              # total size of /var/log
du -sh /*                    # size of each top-level directory
du -h --max-depth=1 /var/    # sizes of subdirectories in /var
du -ah /etc/nginx/ | sort -h # all files sorted by size
```

---

### `free`
🔴 **Tier 1** — Show memory (RAM) usage.

```
Syntax:   free [flags]
```

| Flag | Meaning |
|------|---------|
| `-h` | **Human**-readable |
| `-m` | Show in **megabytes** |
| `-g` | Show in **gigabytes** |
| `-s N` | Update every N **seconds** (watch mode) |

**Examples:**
```bash
free -h          # RAM and swap usage
free -h -s 2     # update every 2 seconds
```

---

### `uname`
🟡 **Tier 2** — Print system information.

```
Syntax:   uname [flags]
```

| Flag | Meaning |
|------|---------|
| `-a` | **All** info |
| `-r` | Kernel **release** (version) |
| `-m` | Machine **architecture** |
| `-n` | **Hostname** |

**Examples:**
```bash
uname -a         # full system info
uname -r         # kernel version
```

---

### `uptime`
🟡 **Tier 2** — Show how long the system has been running + load averages.

```
Syntax:   uptime
```

**Example output:**
```
14:22:01 up 42 days, 3:15, 2 users, load average: 0.12, 0.15, 0.10
#                                                   ^1min  ^5min ^15min
```
Load average: number of processes waiting for CPU. Rule of thumb: should be below number of CPU cores.

---

---

# 8. Network Tools

---

### `ping`
🔴 **Tier 1** — Test network connectivity to a host.

```
Syntax:   ping [flags] host
```

| Flag | Meaning |
|------|---------|
| `-c N` | Send only **N** packets (without -c, it runs forever) |
| `-i N` | Interval of N **seconds** between packets |
| `-t N` | Set **TTL** (time-to-live) |
| `-4` | Force **IPv4** |
| `-6` | Force **IPv6** |

**Examples:**
```bash
ping google.com            # test connectivity (Ctrl+C to stop)
ping -c 4 google.com       # send exactly 4 packets
ping -c 1 192.168.1.1      # check if local gateway is reachable
```

---

### `ssh`
🔴 **Tier 1** — **S**ecure **Sh**ell. Connect to a remote server securely.

```
Syntax:   ssh [flags] user@host
```

| Flag | Meaning |
|------|---------|
| `-p PORT` | Connect to non-standard **port** |
| `-i keyfile` | Use specific **identity** (private key) file |
| `-L local:host:remote` | **Local port forwarding** (tunnel) |
| `-R remote:host:local` | **Remote port forwarding** |
| `-N` | Don't execute a command (**forwarding only**) |
| `-v` | **Verbose** (debug connection issues) |
| `-X` | Enable X11 forwarding (GUI apps over SSH) |

**Examples:**
```bash
ssh alex@192.168.1.100                       # connect by IP
ssh alex@myserver.com                        # connect by hostname
ssh -p 2222 alex@myserver.com               # non-standard port
ssh -i ~/.ssh/my_key.pem user@server        # specific key file
ssh -L 8080:localhost:80 user@server        # forward local port 8080 → server port 80
ssh -N -L 5432:localhost:5432 user@server  # tunnel postgres (no shell)
```

**SSH config file (`~/.ssh/config`)** — saves typing:
```
Host myserver
    HostName 203.0.113.10
    User alex
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
```
Then just: `ssh myserver`

---

### `curl`
🔴 **Tier 1** — **C**lient **URL**. Transfer data to/from a server. HTTP, HTTPS, FTP, and more.

```
Syntax:   curl [flags] URL
```

| Flag | Meaning |
|------|---------|
| `-o filename` | Save output to **file** |
| `-O` | Save with **remote filename** |
| `-L` | Follow **redirects** |
| `-s` | **Silent** (no progress bar) |
| `-I` | Show **headers** only (HEAD request) |
| `-X METHOD` | Set HTTP **method** (GET, POST, PUT, DELETE) |
| `-d "data"` | Send **data** (POST body) |
| `-H "Header: value"` | Add request **header** |
| `-u user:pass` | **Authentication** |
| `-k` | Skip SSL certificate **verification** |
| `-v` | **Verbose** (show request + response headers) |
| `--max-time N` | **Timeout** after N seconds |

**Examples:**
```bash
curl https://example.com                         # fetch a webpage
curl -L https://example.com                      # follow redirects
curl -o myfile.zip https://example.com/file.zip  # download to file
curl -I https://example.com                      # check HTTP headers
curl -X POST -d "key=value" https://api.example.com/endpoint
curl -H "Authorization: Bearer TOKEN" https://api.example.com
curl -s https://api.ipify.org                    # check your public IP
curl -v https://myserver.com                     # debug connection
```

---

### `wget`
🟡 **Tier 2** — Non-interactive web downloader.

```
Syntax:   wget [flags] URL
```

| Flag | Meaning |
|------|---------|
| `-O filename` | Save to specific **file** |
| `-q` | **Quiet** mode |
| `-c` | **Continue** interrupted download |
| `-r` | **Recursive** download |
| `--no-check-certificate` | Skip SSL verification |
| `-P dir` | Save files to **directory** |

**Examples:**
```bash
wget https://example.com/file.tar.gz         # download file
wget -O output.html https://example.com      # save with custom name
wget -c https://example.com/bigfile.iso      # resume interrupted download
wget -q https://example.com/install.sh       # quiet mode
```

---

### `ip`
🟡 **Tier 2** — Show/manage network interfaces and routing. Modern replacement for `ifconfig`.

```
Syntax:   ip [object] [command]
```

| Command | Meaning |
|---------|---------|
| `ip addr` | Show network **interfaces** and IP addresses |
| `ip addr show eth0` | Show specific **interface** |
| `ip route` | Show **routing** table |
| `ip link` | Show link layer (MAC addresses, etc.) |
| `ip -4 addr` | Show only **IPv4** addresses |
| `ip -6 addr` | Show only **IPv6** addresses |

**Examples:**
```bash
ip addr                          # all interfaces + IP addresses
ip addr show eth0                # just eth0
ip -4 addr                       # IPv4 only
ip route                         # routing table
ip route show default            # default gateway
```

---

### `ss`
🟡 **Tier 2** — **S**ocket **S**tatistics. Show network connections. Modern replacement for `netstat`.

```
Syntax:   ss [flags]
```

| Flag | Meaning |
|------|---------|
| `-t` | **TCP** sockets |
| `-u` | **UDP** sockets |
| `-l` | **Listening** sockets only |
| `-n` | **Numeric**: don't resolve hostnames/ports |
| `-p` | Show **process** name and PID |
| `-a` | **All** sockets |

Flags combine: `-tlnp` = TCP + listening + numeric + processes

**Examples:**
```bash
ss -tlnp               # TCP listening ports with process names (most useful)
ss -tulnp              # TCP + UDP listening
ss -an                 # all connections, numeric
ss -tnp | grep :443    # who is connected to port 443
ss -s                  # summary statistics
```

---

### `netstat`
🔵 **Tier 3** — **Net**work **stat**istics. Older tool (still available on many systems).

```
Syntax:   netstat [flags]
```

| Flag | Meaning |
|------|---------|
| `-t` | TCP |
| `-u` | UDP |
| `-l` | Listening |
| `-n` | Numeric |
| `-p` | Process info |
| `-r` | Routing table |

**Examples:**
```bash
netstat -tlnp          # same as ss -tlnp
netstat -rn            # routing table
```

> ℹ️ Prefer `ss` over `netstat` on modern systems; netstat may not be installed.

---

---

# 9. Process Management

---

### `ps`
🔴 **Tier 1** — **P**rocess **S**tatus. Snapshot of currently running processes.

```
Syntax:   ps [flags]
```

Two styles of flags: BSD (no dash) and Unix (with dash). Use BSD style for most cases.

| Flag | Meaning |
|------|---------|
| `aux` | **All** processes, all users, with details |
| `-ef` | All processes in full format (Unix style) |
| `--sort=-%cpu` | Sort by CPU usage |
| `--sort=-%mem` | Sort by memory usage |

**Examples:**
```bash
ps aux                           # all processes
ps aux | grep nginx              # find nginx processes
ps aux --sort=-%cpu | head -10  # top 10 CPU-consuming processes
ps -ef | grep python             # find python processes
```

---

### `top`
🔴 **Tier 1** — Real-time process viewer. Interactive.

```
Syntax:   top
```

**Interactive keys inside top:**
| Key | Action |
|-----|--------|
| `q` | **Quit** |
| `k` | **Kill** a process (enter PID) |
| `P` | Sort by **CPU** usage |
| `M` | Sort by **Memory** usage |
| `u` | Filter by **user** |
| `1` | Show individual **CPU cores** |
| `h` | **Help** |

---

### `htop`
🟡 **Tier 2** — Enhanced interactive process viewer (install with `apt install htop`).

```
Syntax:   htop
```

Better than `top`: color-coded, mouse support, easier to use.

**Interactive keys:**
| Key | Action |
|-----|--------|
| `F2` | **Setup**/settings |
| `F3` | **Search** for process |
| `F5` | Show **tree** view |
| `F6` | **Sort** by column |
| `F9` | **Kill** process |
| `F10` / `q` | **Quit** |
| `u` | Filter by **user** |

---

### `kill`
🔴 **Tier 1** — Send a signal to a process (most commonly to terminate it).

```
Syntax:   kill [signal] PID
         kill -l   (list all signals)
```

| Signal | Number | Meaning |
|--------|--------|---------|
| `SIGTERM` | `-15` | **Graceful** termination (default) |
| `SIGKILL` | `-9` | **Force** kill, cannot be caught or ignored |
| `SIGHUP` | `-1` | Hang up (reload config for many daemons) |
| `SIGSTOP` | `-19` | **Pause** process |
| `SIGCONT` | `-18` | **Resume** paused process |

**Examples:**
```bash
kill 1234                   # graceful kill of PID 1234
kill -9 1234                # force kill (last resort)
kill -HUP 1234              # reload (nginx, sshd, etc.)
kill $(pgrep nginx)         # kill by process name
killall nginx               # kill all processes named 'nginx'
pkill -u alex               # kill all processes by user 'alex'
```

> ℹ️ Always try `kill` (SIGTERM) before `kill -9` (SIGKILL). SIGTERM gives the process a chance to clean up.

---

---

# 10. Shell & Session

---

### `whoami`
🔴 **Tier 1** — Print current username.

```bash
whoami          # → alex
sudo whoami     # → root
```

---

### `history`
🟡 **Tier 2** — Show command history.

```
Syntax:   history [N]
```

| Usage | Meaning |
|-------|---------|
| `history` | Show full history |
| `history 20` | Show last 20 commands |
| `history | grep ssh` | Search history |
| `!!` | Re-run last command |
| `!N` | Re-run command number N |
| `!string` | Re-run last command starting with string |
| `Ctrl+R` | **Interactive reverse search** (most useful!) |

**Examples:**
```bash
history                        # full history
history | grep "nginx"         # find nginx commands
history 50                     # last 50 commands
!234                           # re-run history item 234
```

> ✅ `Ctrl+R` in terminal starts reverse search — type part of a command and it finds it.

---

### `which`
🟡 **Tier 2** — Show the full path of a command.

```
Syntax:   which command
```

**Examples:**
```bash
which python3      # → /usr/bin/python3
which nginx        # → /usr/sbin/nginx
which wg           # check if WireGuard is installed
```

---

### `man`
🟡 **Tier 2** — Display the **man**ual page for a command.

```
Syntax:   man command
```

Navigate with same keys as `less`. Press `q` to quit.

**Examples:**
```bash
man ls             # manual for ls
man 5 crontab      # section 5 (file formats) of crontab manual
man -k "network"   # search manuals for keyword
```

---

---

# 11. System Services (systemd)

*Modern Linux uses `systemd` to manage services (daemons). Services are units described in `.service` files.*

---

### `systemctl`
🔴 **Tier 1** — Control the systemd service manager.

```
Syntax:   systemctl [command] [service]
```

| Command | Meaning |
|---------|---------|
| `start service` | **Start** a service |
| `stop service` | **Stop** a service |
| `restart service` | **Restart** (stop + start) |
| `reload service` | Reload config **without** stopping |
| `enable service` | Enable to start at **boot** |
| `disable service` | Disable from starting at boot |
| `status service` | Show current **status** and recent logs |
| `is-active service` | Check if **active** (returns exit code) |
| `is-enabled service` | Check if enabled for boot |
| `list-units --type=service` | List all **loaded services** |
| `list-units --failed` | Show **failed** units |
| `daemon-reload` | Reload systemd config (after editing .service files) |

**Examples:**
```bash
sudo systemctl start nginx                      # start nginx
sudo systemctl stop nginx                       # stop nginx
sudo systemctl restart nginx                    # restart
sudo systemctl reload nginx                     # reload config (graceful)
sudo systemctl enable nginx                     # enable at boot
sudo systemctl enable --now nginx               # enable + start immediately
sudo systemctl status nginx                     # check status
sudo systemctl list-units --type=service        # all services
sudo systemctl list-units --failed              # failed services
sudo systemctl daemon-reload                    # after editing a .service file
```

---

### `journalctl`
🔴 **Tier 1** — Query the systemd journal (logs).

```
Syntax:   journalctl [flags]
```

| Flag | Meaning |
|------|---------|
| `-u service` | Logs for specific **unit/service** |
| `-f` | **Follow** (live log, like tail -f) |
| `-n N` | Last **N** lines |
| `-e` | Jump to **end** |
| `-r` | **Reverse** (newest first) |
| `-p err` | Filter by **priority** (emerg/alert/crit/err/warning/notice/info/debug) |
| `--since "time"` | Logs **since** a time |
| `--until "time"` | Logs **until** a time |
| `-b` | Logs since last **boot** |
| `--no-pager` | Output without pager (for piping/grep) |

**Examples:**
```bash
sudo journalctl -u nginx                        # all nginx logs
sudo journalctl -u nginx -f                     # live nginx logs
sudo journalctl -u nginx -n 100                 # last 100 nginx log lines
sudo journalctl -p err                          # only errors system-wide
sudo journalctl --since "2024-01-15 10:00:00"  # logs since specific time
sudo journalctl --since "1 hour ago"            # last hour of logs
sudo journalctl -u ssh --since today            # today's SSH logs
sudo journalctl -b                              # logs since last boot
sudo journalctl --no-pager | grep "Failed"     # grep journal output
```

---

---

# 12. Text Editors

---

### `nano`
🔴 **Tier 1** — Simple terminal text editor. Best for quick edits.

```
Syntax:   nano [flags] filename
```

**Key bindings** (shown at the bottom of the screen, `^` = Ctrl):
| Key | Action |
|-----|--------|
| `Ctrl+O` | **Save** (Write Out) — then Enter to confirm |
| `Ctrl+X` | **Exit** |
| `Ctrl+K` | **Cut** line |
| `Ctrl+U` | **Paste** |
| `Ctrl+W` | **Search** |
| `Ctrl+\\` | **Find & Replace** |
| `Ctrl+G` | **Help** |
| `Ctrl+_` | Go to **line number** |
| `Alt+U` | **Undo** |
| `Alt+E` | **Redo** |

| Flag | Meaning |
|------|---------|
| `-l` | Show **line numbers** |
| `+N` | Open at line **N** |

**Examples:**
```bash
nano /etc/hosts                  # edit hosts file
nano +42 /etc/nginx/nginx.conf  # open at line 42
sudo nano /etc/ssh/sshd_config  # edit ssh config (needs sudo)
```

---

### `vim`
🟡 **Tier 2** — Powerful but steep learning curve. Often the only editor available on minimal servers.

```
Syntax:   vim [flags] filename
```

Vim has **modes**: Normal (default), Insert, Visual, Command.

**Essential survival commands:**
| Mode | Keys | Action |
|------|------|--------|
| Normal | `i` | Enter **Insert** mode (type text) |
| Normal | `Esc` | Return to **Normal** mode |
| Normal | `:w` | **Save** |
| Normal | `:q` | **Quit** |
| Normal | `:wq` | **Save and quit** |
| Normal | `:q!` | **Force quit** without saving |
| Normal | `:wq!` | Force save and quit |
| Normal | `u` | **Undo** |
| Normal | `Ctrl+R` | **Redo** |
| Normal | `/pattern` | **Search** forward |
| Normal | `n` | Next search match |
| Normal | `dd` | Delete (cut) current **line** |
| Normal | `yy` | **Yank** (copy) current line |
| Normal | `p` | **Paste** below |
| Normal | `gg` | Go to **beginning** |
| Normal | `G` | Go to **end** |
| Normal | `:[N]` | Go to line **N** |
| Normal | `:%s/old/new/g` | **Replace** all occurrences |

**Examples:**
```bash
vim /etc/nginx/nginx.conf     # edit config
vim +50 file.txt              # open at line 50
```

> ✅ Minimum to survive: `i` to type, `Esc` to stop, `:wq` to save and exit, `:q!` to abandon changes.

---

---

# Quick Reference Cheatsheet

## Most-Used Command Combos

```bash
# Check what's using port 80
ss -tlnp | grep :80

# Watch a log file live
tail -f /var/log/nginx/error.log

# Find and kill a process
ps aux | grep nginx
kill -9 <PID>

# Check disk space
df -h && du -sh /var/*

# Search in all config files
grep -rn "listen 443" /etc/nginx/

# Download and run a script
curl -fsSL https://example.com/install.sh | sudo bash

# Check recent SSH login attempts
sudo journalctl -u ssh --since "1 hour ago" | grep "Failed"

# Archive and compress a directory
tar -czvf backup_$(date +%Y%m%d).tar.gz /var/www/

# Sync local to remote server
rsync -avzP /local/dir/ user@server:/remote/dir/
```

## Pipe Chaining Logic

Linux commands chain with `|` (pipe) — output of one becomes input of the next:

```bash
# Pattern: command | filter | transform | count
cat /var/log/auth.log | grep "Failed" | awk '{print $11}' | sort | uniq -c | sort -rn | head -10
#          ↑ read         ↑ filter       ↑ extract IP     ↑sort  ↑count    ↑ sort by count ↑ top 10
# → Shows top 10 IPs with failed SSH logins
```

## Redirection Quick Reference

| Symbol | Meaning |
|--------|---------|
| `>` | Redirect stdout to file (**overwrite**) |
| `>>` | Redirect stdout to file (**append**) |
| `2>` | Redirect **stderr** to file |
| `2>&1` | Redirect stderr **to stdout** |
| `&>` | Redirect both stdout and stderr |
| `<` | Read stdin **from** file |
| `\|` | Pipe stdout to next command |

```bash
command > output.txt           # save output, overwrite
command >> output.txt          # save output, append
command 2> errors.txt          # save errors only
command > output.txt 2>&1      # save everything
command &> output.txt          # same, shorthand
```
