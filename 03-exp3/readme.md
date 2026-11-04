# Experiment 3: Bash Scripting, Conditionals & Loops

A comprehensive technical manual detailing shell scripting foundations, interpreter directives, command-line parameter bindings, conditional evaluation with test operators, looping algorithms, command substitution, and automated file-system manipulation in Linux.

---

# Part 1 — Core Concepts & Theory

## 1. What is a Script?

A **shell script** is a text file containing a series of commands that are executed by a shell, such as Bash.

While all shells share the same core purpose—taking commands, executing them, and returning output—they can differ in syntax compatibility, interactive user experience, and performance.

### Comparison of Linux Shells

| Dimension | Bash | Zsh | Fish | Dash |
|---|---|---|---|---|
| Standards Compliance | POSIX + GNU extensions | POSIX + extensions | Non-POSIX (custom syntax) | Strictly POSIX |
| Array Indexing | 0-indexed | 1-indexed | 1-indexed | No array support |
| Out-of-the-Box Features | Basic tab completion | Rich completion via modules | Autosuggestions, syntax colors, web config | Barebones |
| Configuration Files | `~/.bashrc`, `~/.bash_profile` | `~/.zshrc` | `~/.config/fish/config.fish` | `~/.profile` |
| Execution Speed | Moderate | Moderate / heavy with plugins | Fast (interactive) | Extremely fast & lightweight |

Scripts are useful for automating repetitive tasks.

Instead of entering several commands manually in the terminal, you can save them in a file and execute them together.

A script can be thought of as a small program made from shell commands.

### Example

```bash
#!/bin/bash
echo "Hello, Linux!"
date
pwd
```

When this script is executed, the commands run in sequence.

---

## 2. What are Command-Line Arguments?

Command-line arguments are values supplied to a program or script when it is started.

They allow the same script to work with different inputs without changing its code.

### General Syntax

```bash
command [argument1] [argument2] ...
```

### Example

```bash
./myscript.sh hello.txt notes folder1
```

In this command:

- `./myscript.sh` is the script being executed.
- `hello.txt` is the first input.
- `notes` is the second input.
- `folder1` is the third input.

Bash makes these values available through **positional parameters**.

---

## 3. Positional Parameters in Bash

Positional parameters are special variables used to access information provided to a script.

| Parameter | Purpose |
|---|---|
| `$0` | Name or path used to invoke the script |
| `$1` | First command-line argument |
| `$2` | Second command-line argument |
| `$3` | Third command-line argument |
| `$#` | Total number of arguments |
| `$@` | All supplied arguments as separate items |

### Example

If the script is executed as:

```bash
./myscript.sh file.txt backup documents
```

Then:

```text
$0 → ./myscript.sh
$1 → file.txt
$2 → backup
$3 → documents
$# → 3
$@ → file.txt backup documents
```

`$@` is particularly useful when processing several arguments, such as inside a loop.

---

## 4. Conditional Testing

Bash provides `test` and `[ ]` for checking conditions.

They are commonly used with `if` statements.

### Common File Tests

| Test | Meaning |
|---|---|
| `-f path` | Checks whether the path exists and refers to a regular file |
| `-d path` | Checks whether the path exists and refers to a directory |

---

### Numeric Comparison

`-ne` means **not equal** and is used when comparing numbers.

### Example

```bash
if [ "$#" -ne 2 ]; then
    echo "Two arguments are required."
fi
```

---

### Integer Comparison Operators

Used specifically for numbers within `[ ]` or `test`.

| Operator | Meaning | Example |
|---|---|---|
| `-eq` | Equal | `[ "$a" -eq "$b" ]` |
| `-ne` | Not equal | `[ "$a" -ne "$b" ]` |
| `-lt` | Less than | `[ "$a" -lt "$b" ]` |
| `-le` | Less than or equal | `[ "$a" -le "$b" ]` |
| `-gt` | Greater than | `[ "$a" -gt "$b" ]` |
| `-ge` | Greater than or equal | `[ "$a" -ge "$b" ]` |

---

### File Attribute Operators

Used for checking file types, states, and permissions.

| Operator | Meaning |
|---|---|
| `-e` | Returns true if the file or directory exists |
| `-f` | Returns true if the path exists and is a regular file |
| `-d` | Returns true if the path exists and is a directory |
| `-s` | Returns true if the file exists and has a size greater than zero |
| `-r` | Returns true if read permission is granted |
| `-w` | Returns true if write permission is granted |
| `-x` | Returns true if execute permission is granted |

---

## 5. Command Substitution

Command substitution allows the output produced by one command to be captured and used elsewhere.

### Syntax

```bash
$(command)
```

### Example

```bash
current_date=$(date)

echo "$current_date"
```

Here, Bash executes `date`, takes its output, and stores that output in `current_date`.

This is useful when the result of one command needs to become the input for another command or be stored in a variable.

---

## 6. Filename Manipulation

Shell commands can be used to modify filenames and rename files.

### Converting Text to Uppercase

The `tr` command can translate lowercase characters into uppercase characters.

```bash
tr '[:lower:]' '[:upper:]'
```

### Example

```bash
echo "hello world" | tr '[:lower:]' '[:upper:]'
```

### Output

```text
HELLO WORLD
```

---

### Renaming or Moving Files

The `mv` command is used to rename a file or move it to another location.

```bash
mv "$old" "$new"
```

### Example

```bash
mv old.txt new.txt
```

This changes the filename from:

```text
old.txt
```

to:

```text
new.txt
```

---

# Part 2 — Conditional Statements and Loops in Bash

## 1. if Statement

The `if` statement is used when a script needs to check a condition and execute a command only when that condition is true.

### Syntax

```bash
if [ condition ]
then
    commands
fi
```

---

### Complete Working Example

```bash
#!/bin/bash

if [ -f "notes.txt" ]
then
    echo "notes.txt is a regular file."
fi
```

---

### Line-by-Line Explanation

| Line | Explanation |
|---|---|
| `#!/bin/bash` | Specifies that the script should be executed using the Bash shell |
| `if [ -f "notes.txt" ]` | Starts the if statement. `-f` checks whether `notes.txt` is a regular file |
| `then` | Indicates that the following command should execute if the condition is true |
| `echo "notes.txt is a regular file."` | Displays a message if `notes.txt` is a regular file |
| `fi` | Marks the end of the if statement |

---

### How to Run

Create the script:

```bash
nano check.sh
```

Create a test file:

```bash
touch notes.txt
```

Give execute permission:

```bash
chmod +x check.sh
```

Run:

```bash
./check.sh
```

### Output

```text
notes.txt is a regular file.
```

---

# 2. if-else Statement

The `if-else` statement is used when there are two possible outcomes.

- If the condition is true → execute the `then` block.
- If the condition is false → execute the `else` block.

### Syntax

```bash
if [ condition ]
then
    commands
else
    commands
fi
```

---

### Complete Working Example

```bash
#!/bin/bash

if [ -f "notes.txt" ]
then
    echo "notes.txt is a regular file."
else
    echo "notes.txt is not a regular file."
fi
```

---

### Line-by-Line Explanation

| Line | Explanation |
|---|---|
| `#!/bin/bash` | Specifies Bash as the interpreter |
| `if [ -f "notes.txt" ]` | Checks whether `notes.txt` is a regular file |
| `then` | Starts the commands that execute when the condition is true |
| `echo "notes.txt is a regular file."` | Prints the message when the file is regular |
| `else` | Runs when the if condition is false |
| `echo "notes.txt is not a regular file."` | Displays a message when the file is not a regular file |
| `fi` | Ends the if-else statement |

---

### How to Test It

If the file exists:

```bash
touch notes.txt
./check.sh
```

### Output

```text
notes.txt is a regular file.
```

Remove the file:

```bash
rm notes.txt
./check.sh
```

### Output

```text
notes.txt is not a regular file.
```

---

# 3. for Loop

A `for` loop is used to repeat a set of commands for multiple values.

Instead of writing the same command several times, a loop performs the operation automatically.

### Syntax

```bash
for variable in values
do
    commands
done
```

---

## while Loop

### Syntax

```bash
while [ condition ]
do
    # commands to execute
done
```

### Complete Working Example

```bash
#!/bin/bash

count=1

while (( count <= 5 ))
do
    echo "$count"
    ((count++))
done
```

---

## for Loop Example

```bash
#!/bin/bash

for name in Ali Rahul Aman
do
    echo "Hello $name"
done
```

### Output

```text
Hello Ali
Hello Rahul
Hello Aman
```

---

## Reading Items from a File

```bash
for item in $(cat fruits.txt)
do
    echo "Fruit: $item"
done
```

---

## Number Range Loop

```bash
#!/bin/bash

for i in {1..5}
do
    echo "Count: $i"
done
```

You can also set step increments:

```bash
{1..10..2}
```

This produces:

```text
1 3 5 7 9
```

---

## C-Style for Loop

```bash
#!/bin/bash

for (( i=1; i<=5; i++ ))
do
    echo "Number: $i"
done
```

---

## Loop Through Files

```bash
#!/bin/bash

for file in *.sh
do
    echo "Found script: $file"
done
```

---

# 4. Arithmetic Operations in Bash

```bash
a=10
b=3

echo "Addition: $((a + b))"
echo "Subtraction: $((a - b))"
echo "Multiplication: $((a * b))"
echo "Division: $((a / b))"
echo "Modulus: $((a % b))"
echo "Exponentiation: $((a ** b))"
```

### Output

```text
Addition: 13
Subtraction: 7
Multiplication: 30
Division: 3
Modulus: 1
Exponentiation: 1000
```

> Division performs integer division and drops decimal values.

---

### Line-by-Line Explanation of for Loop

| Line | Explanation |
|---|---|
| `#!/bin/bash` | Specifies Bash as the interpreter |
| `for name in Ali Rahul Aman` | Starts the loop and processes each value |
| `do` | Marks the beginning of commands belonging to the loop |
| `echo "Hello $name"` | Prints a greeting using the current value |
| `done` | Marks the end of the for loop |

---

### How to Run

Create the script:

```bash
nano greet.sh
```

Give execute permission:

```bash
chmod +x greet.sh
```

Run:

```bash
./greet.sh
```

### Output

```text
Hello Ali
Hello Rahul
Hello Aman
```

---

# Creating and Running a Shell Script

## Step 1: Create the Script

Create a new file using:

```bash
nano script_name.sh
```

Write the required Bash commands inside the file and save it.

---

## Step 2: Give Execute Permission

Before running the script directly:

```bash
chmod +x script_name.sh
```

The `+x` permission allows the file to be executed as a program.

---

## Step 3: Run the Script

Execute the script using:

```bash
./script_name.sh
```

If the script requires command-line arguments:

```bash
./script_name.sh argument1 argument2
```

### Example

```bash
./script_name.sh hello.txt backup
```

The script can access these values through:

```text
$1
$2
```

and other positional parameters.

---

# Task 1 — Check if Argument Is a File or Directory

## Script

```bash
#!/bin/bash

if [ $# -ne 1 ]; then
    echo "No Argument passed, Try Again with an Argument"
    exit 1
fi

target="$1"

if [ -f "$target" ]; then
    echo "$target is a regular file."

elif [ -d "$target" ]; then
    echo "$target is a directory."

elif [ -e "$target" ]; then
    echo "$target exists, but is neither a regular file nor a directory."

else
    echo "Error: $target does not exist."
fi
```

---

## Line-by-Line Explanation

| Line | Explanation |
|---|---|
| `#!/bin/bash` | The shebang — tells Linux to use Bash to execute the script |
| `if [ $# -ne 1 ]; then` | Checks whether exactly one command-line argument was provided |
| `$#` | Represents the number of arguments |
| `-ne` | Means not equal to |
| `echo ...` | Displays instructions to the user |
| `exit 1` | Stops the script with an error status |
| `fi` | Ends the first if statement |
| `target="$1"` | Stores the first argument in the variable `target` |
| `if [ -f "$target" ]` | Checks whether the target is a regular file |
| `elif [ -d "$target" ]` | Checks whether the target is a directory |
| `elif [ -e "$target" ]` | Checks whether the target exists in another form |
| `else` | Handles the case where the path does not exist |
| `fi` | Closes the final conditional structure |

---

## How to Run Task 1

Give permission:

```bash
chmod +x check_type.sh
```

Check a file:

```bash
./check_type.sh /etc/passwd
```

Check a directory:

```bash
./check_type.sh /var/log
```

---

# Task 2 — Rename Files to Uppercase

## Script

```bash
#!/bin/bash

# Loop through all arguments
for file in "$@"
do
    # Convert filename to uppercase and rename
    mv "$file" "$(echo "$file" | tr '[:lower:]' '[:upper:]')"
done
```

---

## Line-by-Line Explanation

### Line 1

```bash
#!/bin/bash
```

This is called the **shebang**.

It tells Linux to use the Bash shell to execute the script.

---

### Comment

```bash
# Loop through all arguments
```

This is a comment.

Comments are ignored by Bash and explain what the code does.

---

### for Loop

```bash
for file in "$@"
```

This starts a `for` loop.

- `for` → Starts the loop.
- `file` → Variable that stores one filename at a time.
- `"$@"` → Represents all command-line arguments supplied to the script.

### Example

```bash
./rename.sh notes.txt report.txt data.txt
```

The loop processes:

```text
notes.txt
report.txt
data.txt
```

one by one.

---

### do

```bash
do
```

Marks the beginning of the commands executed for each argument.

Everything between `do` and `done` belongs to the loop.

---

### Main Rename Command

```bash
mv "$file" "$(echo "$file" | tr '[:lower:]' '[:upper:]')"
```

This is the main command of the script.

It uses `mv` to rename the file.

| Component | Meaning |
|---|---|
| `mv` | Moves or renames files |
| `"$file"` | Represents the original filename |
| `$(...)` | Command substitution |
| `echo "$file"` | Displays the current filename |
| `\|` | Pipe — sends output to another command |
| `tr '[:lower:]' '[:upper:]'` | Converts lowercase characters to uppercase |

### Example

If:

```text
file = notes.txt
```

Then:

```bash
echo "$file" | tr '[:lower:]' '[:upper:]'
```

produces:

```text
NOTES.TXT
```

The complete command effectively becomes:

```bash
mv "notes.txt" "NOTES.TXT"
```

Therefore, the file is renamed.

---

### done

```bash
done
```

Marks the end of the `for` loop.

Bash then takes the next command-line argument and repeats the process.

---

# How to Run the Rename Script

## Step 1: Create the Script

```bash
nano rename.sh
```

Enter the script and save it.

---

## Step 2: Give Execute Permission

```bash
chmod +x rename.sh
```

---

## Step 3: Create Test Files

```bash
touch notes.txt report.txt hello.txt
```

---

## Step 4: Run the Script

```bash
./rename.sh notes.txt report.txt hello.txt
```

The files will be renamed to:

```text
NOTES.TXT
REPORT.TXT
HELLO.TXT
```

---

# Important Point

This script changes only the **filenames**, not the contents of the files.

For example:

```text
notes.txt
```

containing:

```text
hello linux
```

will become:

```text
NOTES.TXT
```

but its contents will still remain:

```text
hello linux
```

---

# Concepts Used

| Code / Command | Purpose |
|---|---|
| `#!/bin/bash` | Specifies Bash as the interpreter |
| `"$@"` | Represents all command-line arguments |
| `for` | Processes arguments one by one |
| `file` | Stores the current argument |
| `mv` | Renames or moves files |
| `echo` | Outputs the filename |
| `\|` | Sends output to another command |
| `tr` | Converts lowercase characters to uppercase |
| `$(...)` | Performs command substitution |
| `do ... done` | Defines the commands inside a loop |

---

# Complete Process Summary

## Shell Scripting

```text
Create Script
      ↓
Add #!/bin/bash
      ↓
Write Commands
      ↓
Save File
      ↓
chmod +x filename.sh
      ↓
Run ./filename.sh
```

## Conditional Statements

```text
if → Check Condition
      ↓
True → Execute then block
      ↓
False → Execute else block
```

## Loops

```text
for → Process multiple values
while → Repeat while a condition is true
```

---

# Key Points to Remember

```text
#!/bin/bash → Bash interpreter

$0 → Script name
$1 → First argument
$2 → Second argument
$# → Number of arguments
$@ → All arguments

-f → Regular file
-d → Directory
-e → Exists

if → Conditional statement
elif → Else if
else → Alternative condition
fi → End of if statement

for → Loop through values
while → Repeat while condition is true
do → Start loop commands
done → End loop

$(command) → Command substitution

tr → Translate characters
mv → Move or rename files
```

---

# Conclusion

This experiment covered the fundamentals of **Bash scripting**, including:

- Shell scripts and the Bash interpreter
- Command-line arguments
- Positional parameters
- Conditional testing
- File test operators
- `if` and `if-else` statements
- `for` and `while` loops
- Arithmetic operations
- Command substitution
- Filename manipulation
- Automated file renaming

These concepts form the foundation for automating tasks and managing files using Bash scripts in Linux.
