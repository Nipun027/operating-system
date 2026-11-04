# Shell Redirection & the sed Stream Editor

---

# 1. Combined Date and User Redirection

To display the current date on the screen while simultaneously redirecting the list of logged-in users to a file, use the semicolon `;`.

## Command

```bash
date; who > logged_users.txt
```

## Explanation

- `date` — Prints the current system date and time to the terminal.
- `;` — Separates two commands so they run sequentially.
- `who` — Lists users currently logged in.
- `> logged_users.txt` — Redirects the output of `who` into a file named `logged_users.txt`.

> **Note:** The `who` command may not work reliably in every environment. Alternatives include `w` or `whoami`.

---

# 2. Basic sed Commands

## What is sed?

`sed` stands for **Stream Editor**.

It is a Linux command-line utility used to read and transform text.

It can perform operations such as:

- Printing selected lines
- Deleting lines
- Searching for text
- Replacing text
- Modifying text using patterns
- Performing automatic text transformations

Unlike an interactive editor such as `nano`, `sed` normally reads input, applies an operation, and displays the resulting output.

---

# 3. Printing Text Using sed

## 3.1 Display the Contents of a File

### Command

```bash
sed -n 'p' Names.txt
```

### Explanation

| Part | Meaning |
|---|---|
| `sed` | Starts the Stream Editor |
| `-n` | Suppresses automatic printing |
| `p` | Prints the selected line |
| `Names.txt` | Input file |

Normally, `sed` automatically prints every line after processing it.

The `-n` option means:

```text
Do not print lines automatically.
```

The `p` command means:

```text
Print.
```

Therefore:

```bash
sed -n 'p' Names.txt
```

prints every line exactly once.

---

## 3.2 Display a Specific Line

### Command

```bash
sed -n '2p' Names.txt
```

This prints only line number `2`.

### Suppose `Names.txt` contains:

```text
John Doe
Alice Smith
Rahul Sharma
Aniket Singh
```

### Output

```text
Alice Smith
```

### Explanation

```text
2p
```

- `2` → Line number 2
- `p` → Print

Therefore:

```text
2p → Print line 2
```

---

## 3.3 Display a Range of Lines

### Command

```bash
sed -n '2,4p' Names.txt
```

This prints lines `2` through `4`.

### Output

```text
Alice Smith
Rahul Sharma
Aniket Singh
```

### Explanation

```text
2,4p
```

- `2` → Starting line
- `4` → Ending line
- `p` → Print

Therefore:

```text
2,4p → Print lines 2 to 4
```

---

# 4. Deleting Lines Using sed

## 4.1 Delete a Specific Line

### Command

```bash
sed '2d' Names.txt
```

This removes line `2` from the output produced by `sed`.

### Suppose the file contains:

```text
John Doe
Alice Smith
Rahul Sharma
Aniket Singh
```

### Output

```text
John Doe
Rahul Sharma
Aniket Singh
```

### Explanation

```text
2d
```

- `2` → Line number 2
- `d` → Delete

> **Important:** This does not permanently remove the line from the original file.

To modify the original file:

```bash
sed -i '2d' Names.txt
```

---

## 4.2 Delete the First and Last Line

### Delete the First Line

```bash
sed '1d' Names.txt
```

### Delete the Last Line

```bash
sed '$d' Names.txt
```

Explanation:

- `1` → First line
- `$` → Last line
- `d` → Delete

---

# 5. Substituting Text

## 5.1 Replace a Word Using sed

### Command

```bash
sed 's/John/Amit/' Names.txt
```

### Suppose the file contains:

```text
John Doe
Alice Smith
Rahul Sharma
```

### Output

```text
Amit Doe
Alice Smith
Rahul Sharma
```

### General Syntax

```bash
s/old_text/new_text/
```

For example:

```bash
s/John/Amit/
```

- `s` → Substitute
- `John` → Text to search
- `Amit` → Replacement text

---

## 5.2 Same Word Appearing Multiple Times

Suppose:

```text
John Doe
John Smith
Rahul John
```

Run:

```bash
sed 's/John/Amit/' Names.txt
```

### Output

```text
Amit Doe
Amit Smith
Rahul John
```

By default, `sed` replaces only the **first occurrence on each line**.

---

## 5.3 Replace All Occurrences

Use the `g` flag.

### Command

```bash
sed 's/John/Amit/g' Names.txt
```

### Output

```text
Amit Doe
Amit Smith
Rahul Amit
```

### Explanation

```text
g → Global
```

It tells `sed` to replace all matching occurrences on each line.

### Comparison

| Command | Effect |
|---|---|
| `sed 's/John/Amit/' Names.txt` | Replaces first occurrence |
| `sed 's/John/Amit/g' Names.txt` | Replaces all occurrences |

---

# 6. Editing the Original File

## Permanently Apply Changes

Normally:

```bash
sed 's/John/Amit/g' Names.txt
```

only displays the modified output.

The original file remains unchanged.

To modify the actual file:

```bash
sed -i 's/John/Amit/g' Names.txt
```

### Explanation

```text
-i → In-place editing
```

### Difference

| Command | Effect |
|---|---|
| `sed 's/John/Amit/g' Names.txt` | Changes only output |
| `sed -i 's/John/Amit/g' Names.txt` | Changes the original file |

---

# 7. Create a Backup Before Editing

## Command

```bash
sed -i.bak 's/John/Amit/g' Names.txt
```

This performs the replacement and creates:

```text
Names.txt
Names.txt.bak
```

The `.bak` file contains the original version.

This is useful when testing commands because you have a backup.

---

# 8. Using Special Characters

## 8.1 Match Text at the Beginning of a Line

Use:

```text
^
```

### Command

```bash
sed 's/^John/Amit/' Names.txt
```

### Suppose:

```text
John Doe
My friend John
John Smith
```

### Output

```text
Amit Doe
My friend John
Amit Smith
```

### Explanation

```text
^ → Beginning of the line
```

Therefore:

```text
^John
```

means:

```text
Match John only when it occurs at the beginning of a line.
```

---

## 8.2 Match Text at the End of a Line

Use:

```text
$
```

### Command

```bash
sed 's/John$/Amit/' Names.txt
```

### Suppose:

```text
John Doe
My friend John
John Smith
```

### Output

```text
John Doe
My friend Amit
John Smith
```

### Explanation

```text
$ → End of the line
```

Therefore:

```text
John$
```

means:

```text
Match John only when it occurs at the end of a line.
```

---

# 9. Working with Spaces

## 9.1 Replace All Spaces with `_`

### Command

```bash
sed 's/ /_/g' Names.txt
```

### Suppose:

```text
John Doe
Alice Smith
Rahul Sharma
```

### Output

```text
John_Doe
Alice_Smith
Rahul_Sharma
```

### Explanation

- `s` → Substitute
- Space → Text being replaced
- `_` → Replacement character
- `g` → All occurrences

---

## 9.2 Replace Only the First Space

### Command

```bash
sed 's/ /_/' Names.txt
```

If the line is:

```text
John Michael Doe
```

The output becomes:

```text
John_Michael Doe
```

Only the first space is replaced.

### Difference

| Command | Effect |
|---|---|
| `sed 's/ /_/' Names.txt` | Replaces first matching space |
| `sed 's/ /_/g' Names.txt` | Replaces every matching space |

---

# 10. Regular Expressions

## 10.1 What Does `.` Mean?

The dot `.` represents **any single character**.

### Suppose:

```text
cat
bat
hat
dog
```

Run:

```bash
sed 's/.at/XXX/' file.txt
```

### Output

```text
XXX
XXX
XXX
dog
```

### Explanation

The pattern:

```text
.at
```

means:

- `.` → Any one character
- `a` → The letter `a`
- `t` → The letter `t`

Therefore, it matches:

```text
cat
bat
hat
```

but not:

```text
dog
```

---

## 10.2 What Does `[^ ]` Mean?

The expression:

```text
[^ ]
```

means:

```text
Match any character that is not a space.
```

When we add `*`:

```text
[^ ]*
```

it means:

```text
Match zero or more characters that are not spaces.
```

This is commonly used to match a word.

---

# 11. Match the First Word of Each Line

## Command

```bash
sed 's/[^ ]*/WORD/' Names.txt
```

### Suppose:

```text
John Doe
Alice Smith
Rahul Sharma
```

### Output

```text
WORD Doe
WORD Smith
WORD Sharma
```

### Explanation

- `[^ ]` → Any character except a space
- `*` → Repeat zero or more times

Therefore:

```text
[^ ]*
```

matches the first word.

---

# 12. Capturing Groups

## What is a Group in sed?

A group allows us to capture a portion of text and use it again in the replacement.

In basic `sed` regular expressions, groups are written as:

```text
\(...\)
```

### Example

```bash
sed 's/\([^ ]*\)/{\1}/' Names.txt
```

If the file contains:

```text
John Doe
Alice Smith
```

The output becomes:

```text
{John} Doe
{Alice} Smith
```

The captured text becomes **Group 1**.

We can refer to Group 1 using:

```text
\1
```

---

# 13. Capture Two Words

## Command

```bash
sed 's/\([^ ]*\) \([^ ]*\)/\1-\2/' Names.txt
```

### Suppose:

```text
John Doe
Alice Smith
```

### Output

```text
John-Doe
Alice-Smith
```

### Understanding the Pattern

```text
\([^ ]*\) \([^ ]*\)
```

contains two groups:

- First group → Captures the first word
- Second group → Captures the second word

The replacement:

```text
\1-\2
```

means:

- `\1` → First captured group
- `-` → Hyphen
- `\2` → Second captured group

Therefore:

```text
John Doe → John-Doe
```

---

# 14. Final Experiment: Swapping Two Words

## How to Swap the First Two Words of Every Line?

### Command

```bash
sed 's/^\([^ ]*\) \([^ ]*\)/\2 \1/' Names.txt
```

### Suppose `Names.txt` contains:

```text
John Doe
Alice Smith
Rahul Sharma
Aniket Singh
```

### Output

```text
Doe John
Smith Alice
Sharma Rahul
Singh Aniket
```

---

## Detailed Explanation

The search pattern is:

```text
^\([^ ]*\) \([^ ]*\)
```

### Break it into Parts

| Pattern | Meaning |
|---|---|
| `^` | Start at the beginning of the line |
| `\([^ ]*\)` | Capture first word as Group 1 |
| Space | Match the space between words |
| `\([^ ]*\)` | Capture second word as Group 2 |

For:

```text
John Doe
```

`sed` identifies:

```text
Group 1 → John
Group 2 → Doe
```

The replacement is:

```text
\2 \1
```

Therefore:

```text
Group 2 + space + Group 1
```

becomes:

```text
Doe John
```

---

# 15. Complete Working Example

## Create the File

```bash
nano Names.txt
```

Enter:

```text
John Doe
Alice Smith
Rahul Sharma
Aniket Singh
```

Save the file.

Run:

```bash
sed 's/^\([^ ]*\) \([^ ]*\)/\2 \1/' Names.txt
```

### Output

```text
Doe John
Smith Alice
Sharma Rahul
Singh Aniket
```

---

# 16. Modify the Original File

To permanently modify `Names.txt`, use:

```bash
sed -i 's/^\([^ ]*\) \([^ ]*\)/\2 \1/' Names.txt
```

Now `Names.txt` itself will contain:

```text
Doe John
Smith Alice
Sharma Rahul
Singh Aniket
```

---

# 17. Important sed Commands and Symbols

| Symbol / Command | Meaning |
|---|---|
| `sed` | Stream Editor |
| `-n` | Suppress automatic printing |
| `p` | Print |
| `d` | Delete |
| `s` | Substitute |
| `g` | Apply replacement globally |
| `-i` | Modify the original file |
| `-i.bak` | Modify file and create a backup |
| `^` | Beginning of line |
| `$` | End of line |
| `.` | Any single character |
| `*` | Zero or more occurrences |
| `[^ ]` | Any character except space |
| `\(...\)` | Capture a group |
| `\1` | First captured group |
| `\2` | Second captured group |

---

# 18. Key Idea to Remember

The final command:

```bash
sed 's/^\([^ ]*\) \([^ ]*\)/\2 \1/' Names.txt
```

can be understood as:

### Find

```text
Beginning → First Word → Space → Second Word
```

### Store

```text
First Word → Group 1
Second Word → Group 2
```

### Replace With

```text
Group 2 → Space → Group 1
```

Therefore:

```text
John Doe → Doe John
```

The most important idea is:

> `sed` first identifies the text using a pattern and then constructs the replacement using captured groups.

---

# 19. Quick Revision

```text
sed     → Stream Editor
-n      → Suppress automatic output
p       → Print
d       → Delete
s       → Substitute
g       → Replace all occurrences
-i      → Edit original file
^       → Beginning of line
$       → End of line
.       → Any character
*       → Zero or more occurrences
\1      → First captured group
\2      → Second captured group
```

---

## Final Important Command

```bash
sed -i 's/^\([^ ]*\) \([^ ]*\)/\2 \1/' Names.txt
```

### Example

```text
John Doe → Doe John
Alice Smith → Smith Alice
```
