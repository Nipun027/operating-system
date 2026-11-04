# Experiment 4: User Session Tracking, Line Ranges & Text Processing

A comprehensive technical manual detailing Unix user session tracking, timestamp epoch conversions, line-range extraction via stream editing, argument shifting, and batch text deletion algorithms in Linux.

---

# Shell Scripting Lab

## Experiment 1 — Determine User Working Period

### Required Command

```bash
sudo apt install coreutils-from-gnu coreutils-from-uutils- --allow-remove-essential
```

## Complete Code

```bash
#!/bin/bash

read -p "Enter username: " user

login_time=$(who | awk -v u="$user" '$1 == u {print $3, $4; exit}')

if [ -z "$login_time" ]
then
    echo "User is not currently logged in."
else
    echo "Login time: $login_time"

    login_seconds=$(date -d "$login_time" +%s)
    current_seconds=$(date +%s)

    total_seconds=$((current_seconds - login_seconds))

    hours=$((total_seconds / 3600))
    minutes=$(((total_seconds % 3600) / 60))

    echo "Total working time: $hours hours $minutes minutes"
fi
```

---

## 1. Login Time Command

The main command used to find the user's login time is:

```bash
login_time=$(who | awk -v u="$user" '$1 == u {print $3, $4; exit}')
```

Let's understand it word by word.

### A. `login_time`

`login_time` is a variable. It stores the user's login date and time.

Example:

```text
login_time = "2026-09-09 10:30"
```

### B. `=`

The `=` sign means **assignment** — store the value on the right inside the variable on the left.

Example:

```bash
a=10
```

### C. `$( )`

This is called **command substitution**.

It runs the command inside the parentheses and captures its output.

Example:

```bash
today=$(date)
```

So:

```bash
login_time=$(who | awk ...)
```

runs the command and stores its output in `login_time`.

> **Remember:** `$()` → Run command + capture output.

---

## 2. `who`

The `who` command shows currently logged-in users.

Example:

```text
aniket pts/0 2026-09-09 10:30
Rahul  pts/1 2026-09-09 11:15
```

Each line has four columns:

| Field | Meaning |
|---|---|
| `$1` | User |
| `$2` | Terminal |
| `$3` | Date |
| `$4` | Time |

For the first row:

```text
$1 = aniket
$2 = pts/0
$3 = 2026-09-09
$4 = 10:30
```

---

## 3. Pipe `|`

In:

```bash
who | awk
```

the pipe `|` sends the output of `who` to `awk`.

In simple words:

> Take the output of `who` and give it to `awk`.

---

## 4. `awk`

`awk` is a text-processing tool.

It reads input line by line and splits each line into fields:

```text
$1, $2, $3, ...
```

---

## 5. `-v`

`-v` creates or sets an AWK variable.

In:

```bash
-v u="$user"
```

an AWK variable called `u` is created and given the value of the Bash variable `$user`.

If:

```text
user=aniket
```

then inside AWK:

```text
u=aniket
```

---

## 6. `u`

This is simply the AWK variable name.

It is short for **user**.

We could also write:

```bash
-v username="$user"
```

---

## 7. `$user`

This is the Bash variable holding the username entered by the user:

```bash
read -p "Enter username: " user
```

If the user enters:

```text
aniket
```

then:

```text
$user = aniket
```

---

## 8. AWK Single Quotes

The single quotes around:

```bash
'$1 == u {print $3, $4; exit}'
```

tell the shell to pass this entire AWK program to AWK without interpreting it.

Everything inside is AWK logic.

---

## 9. `$1`

In AWK, `$1` means the first field of the current line.

Example:

```text
aniket pts/0 2026-09-09 10:30
```

Here:

```text
$1 = aniket
```

---

## 10. `==`

```bash
$1 == u
```

compares two values for equality.

It asks:

> Is the first field equal to the username?

---

## 11. `{ }`

The curly braces contain the action AWK performs when the condition is true.

General structure:

```text
condition { action }
```

Example:

```bash
$1 == u {print $3, $4}
```

Meaning:

> If the first field matches the username, print the date and time.

---

## 12. `print $3, $4`

```bash
print $3, $4
```

prints:

- `$3` → Date
- `$4` → Time

Example output:

```text
2026-09-09 10:30
```

---

## 13. `exit`

```bash
exit
```

tells AWK to stop after finding the first matching user.

---

## 14. Putting the AWK Command Together

```bash
who | awk -v u="$user" '$1 == u {print $3, $4; exit}'
```

Read it as:

> Get currently logged-in users → find the specified username → print that user's date and time.

---

## 15. Final Login-Time Assignment

```bash
login_time=$(who | awk -v u="$user" '$1 == u {print $3, $4; exit}')
```

Ultimately, `login_time` becomes something like:

```text
2026-09-09 10:30
```

### Quick Anatomy

| Part | Meaning |
|---|---|
| `login_time` | Variable to store login time |
| `=` | Assignment |
| `$()` | Execute command and capture output |
| `who` | Show logged-in users |
| `|` | Send output to next command |
| `awk` | Process text |
| `-v` | Create/set AWK variable |
| `u` | AWK variable for username |
| `$user` | Bash variable containing username |
| `$1` | First AWK field |
| `==` | Compare equality |
| `{ }` | AWK action |
| `print` | Display output |
| `$3` | Date |
| `$4` | Time |

---

# Checking Whether the User Is Logged In

```bash
if [ -z "$login_time" ]
```

This condition checks:

> Is `login_time` empty?

- If **YES** → The user is not currently logged in.
- If **NO** → The user is logged in and the script calculates working time.

---

# Unix Timestamps and Time Calculation

```bash
login_seconds=$(date -d "$login_time" +%s)
current_seconds=$(date +%s)
total_seconds=$((current_seconds - login_seconds))
```

---

## 1. The Problem

Suppose a user logged in at:

```text
10:30 AM
```

and the current time is:

```text
2:45 PM
```

The difference is:

```text
4 hours 15 minutes
```

Linux converts both times into seconds, subtracts them, and converts the result back into hours and minutes.

---

## 2. What Is a Unix Timestamp?

A Unix timestamp is the number of seconds that have passed since:

```text
January 1, 1970, 00:00:00 UTC
```

This reference point is known as the **Unix epoch**.

---

## 3. `date +%s`

| Part | Meaning |
|---|---|
| `date` | Linux command for date/time |
| `+%s` | Output the Unix timestamp |

Example:

```bash
date +%s
```

Possible output:

```text
1788957000
```

---

## 4. Why Seconds?

Because subtraction becomes easy.

```text
Login timestamp  = 1000
Current timestamp = 5500
```

Therefore:

```text
4500 seconds
```

---

## 5. Login Time Conversion

```bash
login_seconds=$(date -d "$login_time" +%s)
```

This converts the login date and time into Unix timestamp seconds.

---

## 6. What Does `-d` Do?

| Command | Result |
|---|---|
| `date +%s` | Timestamp for right now |
| `date -d "2026-09-09 10:30" +%s` | Timestamp for the specified date and time |

The `-d` option means:

> Use the date/time provided instead of the current time.

---

## 7. Current Time

```bash
current_seconds=$(date +%s)
```

This gets the Unix timestamp for the current moment.

---

## 8. Subtracting the Timestamps

```bash
total_seconds=$((current_seconds - login_seconds))
```

`$(( ))` performs arithmetic calculations.

Example:

```text
1788972300 - 1788957000 = 15300 seconds
```

---

## 9. Putting It Together

| Quantity | Value |
|---|---|
| Login time | 10:30 AM |
| Current time | 2:45 PM |
| Difference | 15300 seconds |
| Total duration | 4 hours 15 minutes |

### Easy Memory Trick

- `login_seconds` → **WHEN** did they log in?
- `current_seconds` → **WHEN** is it now?
- `total_seconds` → **HOW LONG** have they worked?

---

# Experiment 2 — Display Lines Between Start and End Line Numbers

## Complete Shell Script

```bash
#!/bin/bash

if [ "$#" -ne 3 ]
then
    echo "Usage: $0 filename start_line end_line"
    exit 1
fi

file="$1"
start="$2"
end="$3"

sed -n "${start},${end}p" "$file"
```

## How to Run It

Suppose `names.txt` contains:

```text
Amit
Rahul
Aniket
Rohit
Neha
Priya
Karan
Aman
```

Run:

```bash
./script.sh names.txt 3 6
```

Output:

```text
Aniket
Rohit
Neha
Priya
```

## What the Arguments Mean

| Argument | Meaning |
|---|---|
| `$1` | Filename → `names.txt` |
| `$2` | Start line → `3` |
| `$3` | End line → `6` |

The command:

```bash
sed -n "${start},${end}p" "$file"
```

effectively becomes:

```bash
sed -n "3,6p" "names.txt"
```

Meaning:

> Print lines 3 to 6 from `names.txt`.

---

# Experiment 3 — Delete Lines Containing a Specified Word

## Complete Code

```bash
#!/bin/bash

if [ "$#" -lt 2 ]
then
    echo "Usage: $0 word file1 [file2 ...]"
    exit 1
fi

word="$1"

shift

for file in "$@"
do
    sed -i "/$word/d" "$file"
done
```

---

## Example

Suppose we have:

```text
names.txt
students.txt
teachers.txt
```

Run:

```bash
./script.sh Aniket names.txt students.txt teachers.txt
```

The arguments are:

| Argument | Value |
|---|---|
| `$1` | Aniket |
| `$2` | names.txt |
| `$3` | students.txt |
| `$4` | teachers.txt |

After:

```bash
word="$1"
```

we get:

```text
word = Aniket
```

---

## The `shift` Command

```bash
shift
```

removes the first argument from the argument list.

Before:

```text
Aniket names.txt students.txt teachers.txt
```

After `shift`:

```text
names.txt students.txt teachers.txt
```

These remaining arguments are available through:

```bash
"$@"
```

---

# The `for` Loop

```bash
for file in "$@"
do
    sed -i "/$word/d" "$file"
done
```

This takes each filename from `$@` one at a time.

The sequence is:

```text
names.txt
↓
students.txt
↓
teachers.txt
```

Each filename is temporarily stored in the variable:

```bash
file
```

---

# The Most Important Command

With:

```text
word = Aniket
file = names.txt
```

this command:

```bash
sed -i "/$word/d" "$file"
```

becomes:

```bash
sed -i "/Aniket/d" "names.txt"
```

## Command Breakdown

| Part | Meaning |
|---|---|
| `sed` | Stream editor |
| `-i` | Modify the file directly |
| `/Aniket/` | Find lines containing Aniket |
| `d` | Delete those lines |
| `"$file"` | Current file |

---

# Overall Logic

1. Take a word and multiple files as command-line arguments.
2. Use `shift` to remove the word from the argument list.
3. The remaining arguments are filenames stored in `"$@"`.
4. Loop through each file.
5. Use `sed` to delete every line containing the specified word.

---

## Key Concepts Used

```text
who        → Show currently logged-in users
awk        → Process and filter text
date       → Work with date and time
Unix epoch → Reference point for timestamps
sed        → Stream editor
shift      → Remove the first command-line argument
$@         → All remaining command-line arguments
for        → Process multiple files
```

> This experiment demonstrates practical Linux shell scripting for user session tracking, line extraction, argument processing, and automated batch text modification.
