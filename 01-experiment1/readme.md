# Linux System Administration & Process Management

## 1. Directory Operations

### pwd (Print Working Directory)

**Description:** Prints the full absolute path from the root directory to the current working location.

**Syntax:**

```bash
pwd [OPTION]
```

**Example:**

```bash
pwd
```

**Output:**

```text
/home/ubuntu
```

---

### cd (Change Directory)

**Description:** Navigates the terminal session to a different directory in the filesystem.

**Syntax:**

```bash
cd [DIRECTORY]
```

**System Example:**

```bash
cd /etc/netplan
```

**Key Shortcuts:**

```bash
cd ..        # Move up one level
cd -         # Previous directory
cd ~         # Return to Home Directory
cd           # Return to Home Directory
cd $HOME     # Return to Home Directory
```

---

### mkdir (Make Directory)

**Description:** Creates one or more new directories in the filesystem.

**Syntax:**

```bash
mkdir [OPTION] DIRECTORY...
```

**Standard Creation:**

```bash
mkdir ~/scripts
```

Without `~`:

```bash
mkdir /home/ubuntu/scripts
```

**Useful Options:**

#### `-p` (Parents)

Creates intermediate directories automatically.

```bash
mkdir -p ~/Documents/projects/app/src
```

#### `-v` (Verbose)

Prints confirmation for every created directory.

```bash
mkdir -v ~/Downloads/iso
```

#### `-m` (Mode)

Sets permissions upon creation.

```bash
mkdir -m 700 ~/.ssh
```

**Modifying Permissions of an Existing Folder:**

```bash
chmod 755 ~/Documents/projects
```

---

### rmdir (Remove Directory)

**Description:** Deletes empty directories safely.

**Syntax:**

```bash
rmdir [OPTION] DIRECTORY...
```

**Standard Removal:**

```bash
rmdir ~/Templates
```

**Useful Options:**

#### `-p` (Parents)

Removes empty directory chains.

```bash
rmdir -p ~/Documents/projects/app/src
```

#### `-v` (Verbose)

Confirms deletion.

```bash
rmdir -v ~/Downloads/iso
```

---

# 2. File Operations

## ls (List Directory Contents)

**Description:** Lists directory contents and metadata.

**Syntax:**

```bash
ls [OPTION]... [FILE]...
```

**Long Listing of Home Including Hidden Files:**

```bash
ls -lah ~
```

**Useful Options:**

- `-l` — Long format showing permissions, owner, group, size, and timestamp.
- `-a` — Shows hidden files.
- `-h` — Human-readable file sizes.
- `-t` — Sorts by modification date.
- `-R` — Lists files recursively.

---

## touch

**Description:** Creates an empty file or updates timestamps of an existing file.

**Syntax:**

```bash
touch [OPTION]... FILE...
```

**Standard Creation:**

```bash
touch ~/.bash_aliases
```

### Useful Options

#### `-c` (No Create)

Refreshes timestamp only if the file exists.

```bash
touch -c ~/.bashrc
```

#### `-t` (Custom Timestamp)

```bash
touch -t 202601011200 ~/install.log
```

#### `-d` (Date String)

```bash
touch -d "2026-04-23" ~/release_notes.txt
```

---

## cp (Copy)

**Description:** Copies files and directories from source to destination.

**Syntax:**

```bash
cp [OPTION]... SOURCE... DEST
```

**Standard Copy:**

```bash
cp /etc/skel/.bashrc ~/.bashrc.bak
```

### Useful Options

#### `-r` / `-R` (Recursive)

Copies full directory trees.

```bash
cp -r /etc/netplan ~/backup_netplan
```

#### `-i` (Interactive)

Prompts before overwriting existing files.

```bash
cp -i /etc/hosts ~/hosts
```

#### `-p` (Preserve)

Retains permissions, ownership, and timestamps.

```bash
cp -p /etc/timezone ~/timezone_backup
```

---

## mv (Move / Rename)

**Description:** Moves files or directories to new locations or renames them.

**Syntax:**

```bash
mv [OPTION]... SOURCE... DEST
```

**Standard Move / Rename:**

```bash
mv ~/Desktop ~/Workspace
```

### Useful Options

#### `-i` (Interactive)

```bash
mv -i ~/test.conf /etc/netplan/
```

#### `-n` (No Clobber)

Prevents overwriting.

```bash
mv -n ~/.bashrc.bak ~/.bashrc
```

#### `-v` (Verbose)

```bash
mv -v ~/Downloads/*.deb ~/Packages/
```

---

## rm (Remove)

**Description:** Permanently deletes files and directories.

**Syntax:**

```bash
rm [OPTION]... FILE...
```

> ⚠️ Be careful when using the `rm` command.

**System Directory Example:**

```bash
rm -rf /tmp/apt-*
```

### Useful Options

#### `-r` (Recursive)

Deletes directories and their contents.

```bash
rm -r ~/Downloads/temp_setup/
```

#### `-f` (Force)

Suppresses prompts and missing-file errors.

```bash
rm -f /tmp/lockfile
```

#### `-i` (Interactive)

Asks for confirmation.

```bash
rm -i ~/.bash_history
```

---

## cat (Concatenate)

**Description:** Outputs entire file contents to standard output.

**Syntax:**

```bash
cat [OPTION]... [FILE]...
```

**Example:**

```bash
cat /etc/os-release
```

### Useful Options

#### `-n`

Numbers every line.

```bash
cat -n /etc/shells
```

#### `-b`

Numbers non-empty lines only.

```bash
cat -b /etc/issue
```

---

## head & tail

**Description:** Displays the beginning or end of files.

**Syntax:**

```bash
head -n [N] FILE
tail -n [N] FILE
```

**Examples:**

```bash
head -n 10 /etc/passwd
```

```bash
head -n 5 /etc/fstab
```

```bash
tail -n 25 /var/log/auth.log
```

```bash
tail -n 50 /var/log/dpkg.log
```

---

# 3. IO Redirection & Pipes

## 1. Combining Multiple Files

**Goal:** Merge multiple datasets into a single file.

```bash
cat data1 data2 > data3
```

**Explanation:** Reads the contents of `data1` and `data2` and redirects the combined output into a new file named `data3`.

---

## 2. Extracting Relevant Data from Large Logs

**Goal:** Isolate specific information from a large file.

```bash
grep "Doctor" users.csv > doctor_data
```

**Explanation:** Filters `users.csv`, extracts rows containing `Doctor`, and saves them into `doctor_data`.

---

## 3. File Descriptors and Standard Streams

Every process executed in the shell operates with three default standard streams:

| File Descriptor | Name | Purpose |
|---|---|---|
| `0` | Standard Input (stdin) | Input from keyboard or redirected files |
| `1` | Standard Output (stdout) | Normal command output |
| `2` | Standard Error (stderr) | Error messages |

---

## 4. Overwrite (`>`) vs Append (`>>`)

### Overwriting (`>`)

Deletes existing content and writes new data.

```bash
echo "hello" > myfile
```

### Appending (`>>`)

Adds new data without deleting existing content.

```bash
echo "buddy" >> myfile
```

Result:

```text
hello
buddy
```

---

## 5. Redirection of Errors (`2>`)

To redirect error output:

```bash
cd /root/ 2> error.txt
```

The error message is redirected into `error.txt`.

---

## 6. Combined Redirection of Output and Errors (`&>`)

Redirects both normal output and errors.

```bash
command &> output.txt
```

---

## 7. Standard Input Redirection (`<`)

Passes file contents as input.

```bash
cat < data1
```

---

# 4. Pipes, Tee, and Xargs

## Combining and Sorting Datasets

```bash
cat names.txt countries.txt | sort
```

Combines the files and sorts the output alphabetically.

---

## Extracting Unique Records

```bash
cat names.txt | sort | uniq
```

`uniq` works on adjacent lines, so sorting first ensures duplicates are grouped together.

---

## Viewing a Specific Line Range

Display lines 31 through 37:

```bash
cat data.txt | head -n 37 | tail -n 7
```

---

## The tee Command

Copies standard input to both the terminal and a file.

```bash
ls | tee test.txt
```

---

## The xargs Command

Converts standard input into command arguments.

```bash
cat file_names.txt | xargs touch
```

---

# GREP (Global Regular Expression Print)

Terminal-based search tool similar to Ctrl+F.

## Ignore Case

```bash
grep -i "error" logs.txt
```

## Invert Matches

```bash
grep -v "error" logs.txt
```

## Count Matching Lines

```bash
grep -c "Doctor" users.csv
```

## Search Exact Words

```bash
grep -w "Carly" users.csv
```

## Show Line Numbers

```bash
grep -n "Carly" users.csv
```

---

# 5. Mastering the Linux find Command

The `find` command is used to search for files and directories.

## Syntax

```bash
find [options] [path] [expression]
```

- **Options:** Add additional functionality.
- **Path:** Specifies the directory to search.
- **Expression:** Defines search filters or patterns.
- `.` represents the current directory.

---

## Search by File Size

```bash
find . -size 4M
```

Common suffixes:

- `M` — Megabytes
- `k` — Kilobytes
- `G` — Gigabytes
- `b` — Blocks/bytes depending on implementation

---

## Search by Exact Name

```bash
find . -name "a123"
```

This search is case-sensitive.

---

## Case-Insensitive Search

```bash
find . -iname "a123"
```

Matches different capitalization.

---

## Search by File Owner

```bash
find . -user root
```

---

## Find Empty Files and Directories

```bash
find . -empty
```

---

# 6. Text Editors: Vi / Vim & Nano

## Vi / Vim Editor

A modal terminal text editor.

**Open a file:**

```bash
vim [FILE]
```

### Core Operations

| Command | Function |
|---|---|
| `i` | Enter Insert mode |
| `Esc` | Return to Normal mode |
| `:w` | Save |
| `:q` | Quit |
| `:wq` | Save and Quit |
| `:q!` | Force Quit without saving |
| `dd` | Delete current line |
| `yy` | Copy current line |
| `p` | Paste |
| `u` | Undo |
| `/pattern` | Search forward |

---

## Nano Editor

A modeless terminal text editor.

**Open a file:**

```bash
nano [FILE]
```

### Core Shortcuts

| Shortcut | Function |
|---|---|
| `Ctrl + O` | Save file |
| `Ctrl + X` | Exit editor |
| `Ctrl + W` | Search text |
| `Ctrl + K` | Cut current line |
| `Ctrl + U` | Paste line |
| `Ctrl + _` | Jump to line number |

---

# 7. Linux Process Management

## 1. jobs

**What is it?** Displays jobs managed by the current shell.

**Example:**

```bash
sleep 100 &
jobs
```

Possible output:

```text
[1]+ Running sleep 100 &
```

- `[1]` → Job ID
- `Running` → Job status
- `sleep 100` → Command

**Remember:** `jobs` = Show jobs managed by the current shell.

---

## 2. bg

**What is it?** Resumes a stopped job in the background.

**Example:**

```bash
bg
```

Specific job:

```bash
bg %2
```

Process flow:

```text
Running → Ctrl + Z → Stopped → bg → Running in background
```

**Remember:** `bg` = Continue a stopped job in the background.

---

## 3. fg

Brings a background or stopped job to the foreground.

```bash
fg
```

Specific job:

```bash
fg %2
```

**Remember:** `fg` = Bring a job to the foreground.

---

## 4. ps

`ps` stands for Process Status.

```bash
ps
```

Important columns:

- `PID` — Process ID
- `TTY` — Terminal
- `TIME` — CPU time used
- `CMD` — Command

**Remember:** `ps` = View process information.

---

## 5. ps -e

Shows all processes on the system.

```bash
ps -e
```

**Remember:** `ps -e` = Show all processes.

---

## 6. ps -ef

Shows all processes in full-format detail.

```bash
ps -ef
```

Important columns:

- `UID` — User who owns the process
- `PID` — Process ID
- `PPID` — Parent Process ID
- `C` — CPU-related field
- `STIME` — Process start time
- `TTY` — Terminal
- `TIME` — CPU time consumed
- `CMD` — Command used to start the process

**Remember:** `ps -ef` = All processes + detailed information + parent/child relationship.

---

## 7. ps -p PID

Displays a specific process.

```bash
ps -p PID
```

Example:

```bash
ps -p 6721
```

Detailed version:

```bash
ps -p 6721 -f
```

**Remember:** `ps -p PID` = Check a specific process.

---

## 8. ps -o pid,ni,cmd

Displays selected process columns.

```bash
ps -o pid,ni,cmd
```

- `pid` = Process ID
- `ni` = Nice value
- `cmd` = Command

---

## 9. ps aux

Shows processes with CPU and memory information.

```bash
ps aux
```

Important columns:

- `USER`
- `PID`
- `%CPU`
- `%MEM`
- `STAT`
- `COMMAND`

**Remember:** `ps aux` = View processes with CPU and memory usage.

---

## 10. kill

Sends a signal to a process.

```bash
kill PID
```

By default, it sends `SIGTERM` (signal 15).

**Remember:** `kill` = Send a signal to a process.

---

## 11. kill -15 PID

Sends `SIGTERM`.

```bash
kill -15 PID
```

This gives the application an opportunity to clean up before exiting.

**Remember:** `SIGTERM` = Graceful termination request.

---

## 12. kill -9 PID

Sends `SIGKILL`.

```bash
kill -9 PID
```

Forces immediate termination.

> ⚠️ The application does not get an opportunity to perform cleanup.

**Remember:** `kill -9` = Forcefully terminate a process.

---

## 13. kill -l

Lists available signals.

```bash
kill -l
```

Important signals:

- `1` — SIGHUP
- `2` — SIGINT
- `9` — SIGKILL
- `15` — SIGTERM
- `18` — SIGCONT
- `19` — SIGSTOP

---

## 14. kill -STOP PID

Suspends a running process without terminating it.

```bash
kill -STOP PID
```

Process flow:

```text
RUNNING → SIGSTOP → STOPPED
```

**Remember:** `SIGSTOP` = Stop or pause a process.

---

## 15. kill -CONT PID

Continues a stopped process.

```bash
kill -CONT PID
```

Process flow:

```text
STOPPED → SIGCONT → RUNNING
```

**Remember:** `SIGCONT` = Continue a stopped process.

---

## 16. nice

Starts a process with a specified nice value.

```bash
nice -n 10 sleep 300 &
```

Nice value range:

```text
-20 → More favorable priority
  0 → Default priority
+19 → Less favorable priority
```

**Remember:** Lower nice value = More favorable scheduling priority.

---

## 17. renice

Changes the nice value of an already-running process.

```bash
renice 10 -p PID
```

### nice vs renice

- `nice` — Set nice value when starting a process.
- `renice` — Change nice value of an existing process.

---

## 18. top

Live and continuously updating view of processes and system resources.

```bash
top
```

### Interactive Commands

| Key | Function |
|---|---|
| `q` | Exit |
| `k` | Kill a process |
| `c` | Toggle command display |
| `u` | Filter by username |
| `f` | Manage fields |
| `z` | Toggle color display |
| `b` | Toggle highlighting of running processes |

**Remember:** `top` = Live monitoring of processes and system resources.

---

# 8. Complete Process Management Picture

## Job Control

```text
jobs → See shell jobs
bg → Continue stopped job in background
fg → Bring job to foreground
```

## Process Inspection

```text
ps → Current session processes
ps -e → All processes
ps -ef → Detailed process information with PPID
ps -p PID → One specific process
ps aux → Processes with CPU/RAM information
```

## Process Control

```text
kill PID → SIGTERM (graceful)
kill -15 PID → Graceful termination
kill -9 PID → Forceful termination
kill -STOP PID → Stop/pause
kill -CONT PID → Continue
kill -l → List signals
```

## Process Priority

```text
nice → Set priority when starting
renice → Change priority after starting
```

## Live Monitoring

```text
top → Continuously monitor processes and system resources
```

---

# Quick Revision

> **jobs** = Shell job management  
> **bg** = Continue stopped job in background  
> **fg** = Bring job to foreground  
> **ps** = Process information  
> **kill** = Send signals to processes  
> **nice / renice** = Manage process priority  
> **top** = Live process monitoring  
> **grep** = Search text  
> **find** = Search files  
> **`|`** = Pipe output to another command  
> **`>`** = Overwrite output  
> **`>>`** = Append output  
> **`2>`** = Redirect errors
