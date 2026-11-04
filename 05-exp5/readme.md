# Experiment 5: String Operations in Shell Script & C

A comprehensive technical manual analyzing dual-paradigm string manipulation in Linux, comparing high-level Bash parameter expansion against character-array processing in C, and comparing execution profiles using the `time` utility.

---

# EXPERIMENT 5

## String Length and Substring Extraction in Shell Script and C

## 1. Aim

To write a shell script and an equivalent C program that each:

- Find the length of a string entered by the user.
- Extract a substring from that string using a starting position and number of characters.
- Compare the execution time of both programs using the `time` command.

---

# Part A — Shell Script (`exp7.sh`)

## Program

```bash
#!/bin/bash

read -p "Enter a string: " str

echo "Length of string: ${#str}"

read -p "Enter starting position: " start

read -p "Enter number of characters: " length

echo "Substring: ${str:$start:$length}"
```

---

## Line-by-Line Explanation

### Step 1 — The Shebang

```bash
#!/bin/bash
```

This first line tells Linux which interpreter should run the file — in this case, the Bash shell.

---

### Step 2 — Taking the String as Input

```bash
read -p "Enter a string: " str
```

- `read` → Takes input from the user.
- `-p` → Displays a prompt before reading.
- `"Enter a string: "` → The prompt shown on the screen.
- `str` → The variable where the input is stored.

For example, if the user enters:

```text
hello world
```

then:

```text
str="hello world"
```

---

### Step 3 — Finding the Length

```bash
echo "Length of string: ${#str}"
```

The important part is:

```bash
${#str}
```

- `#` → Asks Bash for the length.
- `str` → The variable being measured.

It returns the number of characters stored in `str`.

| Input String | Length |
|---|---:|
| `hello` | 5 |
| `hello world` | 11 |

---

### Step 4 — Reading the Starting Position

```bash
read -p "Enter starting position: " start
```

If the user enters:

```text
2
```

then:

```text
start=2
```

---

### Step 5 — Reading the Number of Characters

```bash
read -p "Enter number of characters: " length
```

If the user enters:

```text
5
```

then:

```text
length=5
```

---

### Step 6 — Extracting the Substring

```bash
echo "Substring: ${str:$start:$length}"
```

The general syntax is:

```text
${string:start:length}
```

| Part | Meaning |
|---|---|
| `string` | Variable containing the string |
| `start` | Starting position |
| `length` | Number of characters to extract |

It means:

> Start from position `$start` and take `$length` characters.

### Example

| Variable | Value |
|---|---|
| `str` | `hello world` |
| `start` | `2` |
| `length` | `5` |
| Result | `llo w` |

---

## Flow of the Script

```text
STRING
   ↓
Find its length
   ↓
Enter starting position
   ↓
Enter number of characters
   ↓
Extract substring
   ↓
OUTPUT
```

---

## Executing the Script

Give execute permission:

```bash
chmod +x exp7.sh
```

Run the script with the `time` command:

```bash
time ./exp7.sh
```

`chmod +x` grants execute permission to the file, and `time` reports how long the script took to run.

---

## Sample Output

```text
Enter a string: OperatingSystem
Length of string: 15
Enter starting position: 9
Enter number of characters: 6
Substring: System
```

> **Note:** Bash string positions start from `0`. Therefore, in `OperatingSystem`, the letter `O` is at position `0`, and `S` in `System` is at position `9`.

---

# Part B — C Program (`exp7.c`)

## Program

```c
#include <stdio.h>

int main()
{
    char str[100];
    int start, length, i, count = 0;

    printf("Enter a string: ");
    fgets(str, 100, stdin);

    while (str[count] != '\0' && str[count] != '\n')
    {
        count++;
    }

    printf("Length of string: %d\n", count);

    printf("Enter starting position: ");
    scanf("%d", &start);

    printf("Enter number of characters: ");
    scanf("%d", &length);

    printf("Substring: ");

    for (i = start; i < start + length && i < count; i++)
    {
        printf("%c", str[i]);
    }

    printf("\n");

    return 0;
}
```

---

## Line-by-Line Explanation

### Step 1 — Header File

```c
#include <stdio.h>
```

- `#include` → Includes a library in the program.
- `stdio.h` → Standard Input/Output library.

It is required for:

```text
printf()
scanf()
fgets()
```

---

### Step 2 — The `main()` Function

```c
int main()
{
```

- `main()` → Program execution begins here.
- `int` → Indicates that the function returns an integer value.

---

### Step 3 — Declaring the Variables

```c
char str[100];
int start, length, i, count = 0;
```

| Variable | Purpose |
|---|---|
| `str[100]` | Character array that holds the string |
| `start` | Starting position of the substring |
| `length` | Number of characters required |
| `i` | Loop variable |
| `count` | Counter used to calculate string length |

---

### Step 4 — Taking the String as Input

```c
printf("Enter a string: ");
fgets(str, 100, stdin);
```

The three arguments of `fgets()` are:

```text
fgets(str, 100, stdin)
      ↓    ↓      ↓
    array size   input
```

- `str` → Where the input is stored.
- `100` → Maximum number of characters to read.
- `stdin` → Standard input, usually the keyboard.

If the user enters:

```text
hello world
```

`fgets()` stores the text along with the newline and terminating null character.

---

### Step 5 — Finding the String Length

```c
while (str[count] != '\0' && str[count] != '\n')
{
    count++;
}
```

The loop checks every character and stops when it reaches:

- `'\n'` → Newline
- `'\0'` → Null character

Example:

```text
h e l l o \n \0
0 1 2 3 4 5  6
```

| Input String | Value of `count` |
|---|---:|
| `hello` | 5 |
| `hello world` | 11 |

---

### Step 6 — Displaying the Length

```c
printf("Length of string: %d\n", count);
```

- `%d` → Prints an integer.
- `count` → Holds the calculated length.
- `\n` → Moves to the next line.

---

### Step 7 — Taking the Starting Position

```c
printf("Enter starting position: ");
scanf("%d", &start);
```

- `%d` → Reads an integer.
- `&start` → Address of the variable where `scanf()` stores the value.

If the user enters `2`:

```text
start = 2
```

---

### Step 8 — Taking the Substring Length

```c
printf("Enter number of characters: ");
scanf("%d", &length);
```

If the user enters:

```text
5
```

then:

```text
length = 5
```

---

### Step 9 — Printing the Substring

```c
for (i = start; i < start + length && i < count; i++)
{
    printf("%c", str[i]);
}
```

Suppose:

```text
str = hello world
start = 2
length = 5
```

The loop runs for:

```text
i = 2, 3, 4, 5, 6
```

String positions:

```text
h e l l o _ w o r l d
0 1 2 3 4 5 6 7 8 9 10
    ↑         ↑
  start       end
```

Output:

```text
llo w
```

The `%c` format specifier prints one character.

The condition:

```c
i < count
```

prevents the program from reading past the end of the string.

---

### Step 10 — End of the Program

```c
return 0;
}
```

- `return 0` → Tells the operating system that the program completed successfully.
- `}` → Marks the end of the `main()` function.

---

# Compiling and Running the C Program

Compile:

```bash
gcc exp7.c -o exp7
```

Run:

```bash
./exp7
```

---

## Sample Output

```text
Enter a string: OperatingSystem
Length of string: 15
Enter starting position: 9
Enter number of characters: 6
Substring: System
```

---

# Comparing the Execution Time

Run each program using the `time` command:

### C Program

```bash
time ./exp7
```

### Shell Script

```bash
time ./exp7.sh
```

---

# Reading the Output of `time`

The output generally looks like:

```text
real    0m0.005s
user    0m0.001s
sys     0m0.004s
```

| Field | Meaning |
|---|---|
| `real` | Total elapsed wall-clock time |
| `user` | CPU time spent executing user-level code |
| `sys` | CPU time spent in kernel/system calls |

---

## 1. `real`

```text
real 0m0.005s
```

Real time is the actual elapsed time from when the program starts until it finishes.

```text
0.005 seconds = 5 milliseconds
```

Think of it as a stopwatch:

```text
START ─────────────────► END
          0.005 sec
```

---

## 2. `user`

```text
user 0m0.001s
```

This is the amount of CPU time spent executing the program's code in **user space**.

```text
0.001 seconds = 1 millisecond
```

Calculations, loops, and string operations generally contribute to user CPU time.

---

## 3. `sys`

```text
sys 0m0.004s
```

This is the amount of time the program spent in the Linux kernel on its behalf.

Operations involving system calls such as:

```text
read
write
open
close
```

can contribute to `sys` time.

```text
0.004 seconds = 4 milliseconds
```

---

# Will the Execution Time Remain the Same?

**No.**

The time can change every time you run the program.

This can happen because of:

- CPU scheduling
- Cache state
- Disk or terminal I/O
- System load
- Other running programs
- Virtual machine overhead

---

# Note for Ubuntu on VirtualBox

If Ubuntu is running inside VirtualBox, small variations in execution time are especially normal because the virtual machine shares the physical CPU and system resources with the host computer.

---

# Key Concepts Used

```text
Bash Parameter Expansion → ${#str}
String Length           → Number of characters
Substring Extraction    → ${str:start:length}
C Character Array       → char str[100]
fgets()                 → Read a string
while Loop              → Calculate string length
for Loop                → Print the substring
gcc                     → Compile the C program
time                    → Measure execution time
```

---

## Conclusion

This experiment demonstrates string operations using two different approaches:

- **Bash** uses built-in parameter expansion for concise string processing.
- **C** uses character arrays and loops for explicit character-by-character processing.
- The `time` utility can be used to observe the execution profile of both programs.
