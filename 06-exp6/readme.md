# Experiment 6: Memory Allocation & Virtual Memory in C

## Aim

To write a C program that:

- Takes a memory size in MB from the command line.
- Allocates that amount of memory dynamically.
- Touches each memory page so that physical memory is actually used.
- Keeps the program running so memory usage can be observed.
- Releases memory when the program is terminated.

---

# 1. Introduction

This experiment demonstrates important concepts of **Linux Virtual Memory Management**.

The program:

1. Takes memory size as a command-line argument.
2. Allocates virtual memory using `malloc()`.
3. Touches every memory page.
4. Keeps the memory allocated.
5. Allows us to observe memory usage using `free -h`.
6. Releases memory when the program exits.

### Main Concepts

- Dynamic memory allocation
- Virtual memory
- Physical RAM
- Demand paging
- Page faults
- Command-line arguments
- Signals
- Memory monitoring

---

# 2. The Complete Program

Create a file named `memory.c`.

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char *argv[])
{
    int mb;

    if (argc != 2)
    {
        printf("Usage: %s <memory_in_MB>\n", argv[0]);
        return 1;
    }

    mb = atoi(argv[1]);

    char *memory = malloc(mb * 1024 * 1024);

    if (memory == NULL)
    {
        printf("Memory allocation failed\n");
        return 1;
    }

    for (int i = 0; i < mb * 1024 * 1024; i += 4096)
    {
        memory[i] = 1;
    }

    printf("%d MB memory allocated.\n", mb);
    printf("Program is running... Press Ctrl+C to exit.\n");

    while (1)
    {
        sleep(1);
    }

    return 0;
}
```

---

# 3. Compiling and Running the Program

## Compile

```bash
gcc memory.c -o memory
```

## Run

For example, to allocate approximately **500 MB**:

```bash
./memory 500
```

Here:

| Part | Meaning |
|---|---|
| `./memory` | The compiled program |
| `500` | Amount of memory requested in MB |

---

# 4. Monitoring Memory Usage

While the program is running, open another terminal.

Run:

```bash
free -h
```

For continuously updated memory information:

```bash
watch -n 1 free -h
```

### Why Use Another Terminal?

The program continues running indefinitely to keep the allocated memory active.

Therefore, another terminal is used to observe system memory usage.

---

# 5. Header Files

The program uses three header files.

| Header File | Provides | Used For |
|---|---|---|
| `stdio.h` | `printf()` | Displaying messages |
| `stdlib.h` | `malloc()`, `atoi()`, `free()` | Memory allocation and conversion |
| `unistd.h` | `sleep()` | Pausing the program |

---

# 6. argc and argv

The main function is written as:

```c
int main(int argc, char *argv[])
```

## argc

`argc` means **argument count**.

It tells us how many command-line arguments were provided.

## argv

`argv` means **argument vector**.

It stores the command-line arguments as strings.

Example:

```bash
./memory 500
```

| Value | Meaning |
|---|---|
| `argc` | `2` |
| `argv[0]` | `./memory` |
| `argv[1]` | `500` |

Note that `argv[1]` is initially stored as a **string**.

---

# 7. Checking the Number of Arguments

The program checks whether exactly one argument was provided.

```c
if (argc != 2)
{
    printf("Usage: %s <memory_in_MB>\n", argv[0]);
    return 1;
}
```

The program name counts as one argument.

Therefore:

```bash
./memory 500
```

gives:

```text
argc = 2
```

If the user runs:

```bash
./memory
```

then:

```text
argc = 1
```

The program displays the correct usage and exits.

---

# 8. Converting the Memory Size

The following line converts the command-line argument into an integer:

```c
mb = atoi(argv[1]);
```

For example:

```text
argv[1] = "500"
```

After `atoi()`:

```text
mb = 500
```

`atoi()` means **ASCII to Integer**.

---

# 9. Allocating Memory

Memory is allocated using:

```c
char *memory = malloc(mb * 1024 * 1024);
```

If:

```text
mb = 500
```

then the program requests:

```text
500 × 1024 × 1024 bytes
```

which is approximately:

```text
524,288,000 bytes
```

---

## Why `char *`?

`malloc()` returns the address of the allocated memory.

Therefore, we store it in a pointer:

```c
char *memory
```

The pointer stores the starting address of the allocated memory.

Conceptually:

```text
memory
   |
   v

+------+------+------+------+------+
| [0]  | [1]  | [2]  | [3]  | ...  |
+------+------+------+------+------+
```

Because a `char` occupies one byte, `memory[i]` can access individual bytes.

---

# 10. Checking Whether Allocation Succeeded

After calling `malloc()`, the program checks:

```c
if (memory == NULL)
{
    printf("Memory allocation failed\n");
    return 1;
}
```

If memory allocation fails, `malloc()` returns:

```text
NULL
```

The program then displays an error message and exits safely.

---

# 11. Actually Using the Memory

The program contains the following loop:

```c
for (int i = 0; i < mb * 1024 * 1024; i += 4096)
{
    memory[i] = 1;
}
```

This is an important part of the experiment.

`malloc()` requests **virtual memory**, but Linux may not immediately assign physical RAM to every part of the allocated memory.

The program writes to memory every:

```text
4096 bytes
```

which is commonly a **4 KB page size**.

Writing one byte per page causes the operating system to allocate backing memory for those pages.

Conceptually:

```text
Virtual Pages:

[Page 0] [Page 1] [Page 2] [Page 3] ...
    |        |        |        |
    v        v        v        v

memory[i] = 1

    |        |        |        |
    v        v        v        v

Physical RAM Pages
```

---

# 12. Virtual Memory vs Physical Memory

A successful:

```c
malloc()
```

does not necessarily mean that all requested memory is immediately backed by physical RAM.

The process first receives virtual address space.

When the program accesses memory, the operating system can allocate physical memory pages as required.

### Main Concept

```text
Request Virtual Memory
        ↓
Access Memory
        ↓
Operating System Handles Page
        ↓
Physical RAM Backs the Page
```

This is the main virtual memory concept demonstrated by the experiment.

---

# 13. Displaying the Result

The following line displays the allocated memory:

```c
printf("%d MB memory allocated.\n", mb);
```

Example:

```text
500 MB memory allocated.
```

---

# 14. Keeping the Program Running

The program uses:

```c
while (1)
{
    sleep(1);
}
```

The condition:

```c
while (1)
```

is always true.

Therefore, the program continues running indefinitely.

`sleep(1)` pauses the program for one second during every iteration.

This prevents the program from unnecessarily using CPU resources while keeping the allocated memory active.

---

# 15. Observing the Memory

While the program is running:

```bash
./memory 500
```

open another terminal and run:

```bash
free -h
```

For continuous monitoring:

```bash
watch -n 1 free -h
```

This allows the user to observe changes in system memory.

---

# 16. Converting MB to Bytes

A cleaner method is:

```c
size_t size = (size_t)mb * 1024 * 1024;
```

Then memory can be allocated using:

```c
char *memory = malloc(size);
```

For example:

```text
500 MB
   ↓
500 × 1024 × 1024
   ↓
524,288,000 bytes
```

`size_t` is commonly used for memory sizes because it is designed to store object sizes safely.

---

# 17. The Complete Flow of the Program

```text
./memory 500
      |
      v
argc = 2
argv[1] = "500"
      |
      v
atoi()
      |
      v
mb = 500
      |
      v
malloc(500 × 1024 × 1024)
      |
      v
Check memory != NULL
      |
      v
Touch every 4096-byte page
      |
      v
Memory becomes actively used
      |
      v
while(1) + sleep(1)
      |
      v
Observe using free -h
      |
      v
Ctrl+C
      |
      v
Program terminates
```

---

# 18. Memory Release

When a process terminates, the operating system automatically reclaims its memory.

In the basic version of this program, pressing:

```text
Ctrl+C
```

terminates the program.

Linux then releases the resources associated with that process.

A more advanced version can explicitly handle the `SIGINT` signal and release memory before exiting.

---

# 19. Releasing Memory Using Ctrl+C

We can use a signal handler to catch `Ctrl+C`.

First include:

```c
#include <signal.h>
```

Create a global pointer:

```c
char *memory;
```

Then create a signal handler:

```c
void handle_sigint(int sig)
{
    free(memory);
    printf("\nMemory released.\n");
    exit(0);
}
```

Register the signal handler:

```c
signal(SIGINT, handle_sigint);
```

### Signal Flow

```text
Ctrl+C
   |
   v
SIGINT
   |
   v
handle_sigint()
   |
   v
free(memory)
   |
   v
exit(0)
```

---

# 20. Complete Program with Signal Handling

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>

char *memory;

void handle_sigint(int sig)
{
    free(memory);
    printf("\nMemory released.\n");
    exit(0);
}

int main(int argc, char *argv[])
{
    int mb;

    if (argc != 2)
    {
        printf("Usage: %s <memory_in_MB>\n", argv[0]);
        return 1;
    }

    signal(SIGINT, handle_sigint);

    mb = atoi(argv[1]);

    size_t size = (size_t)mb * 1024 * 1024;

    memory = malloc(size);

    if (memory == NULL)
    {
        printf("Memory allocation failed.\n");
        return 1;
    }

    for (int i = 0; i < mb * 1024 * 1024; i += 4096)
    {
        memory[i] = 1;
    }

    printf("Memory allocated successfully.\n");
    printf("Press Ctrl+C to release memory and exit.\n");

    while (1)
    {
        sleep(1);
    }

    return 0;
}
```

---

# 21. Compile and Run the Updated Program

Compile:

```bash
gcc memory.c -o memory
```

Run with 500 MB:

```bash
./memory 500
```

The program displays:

```text
Memory allocated successfully.
Press Ctrl+C to release memory and exit.
```

When you press:

```text
Ctrl+C
```

the program displays:

```text
Memory released.
```

---

# 22. Key Concepts

| Concept | Meaning |
|---|---|
| `argc` | Number of command-line arguments |
| `argv` | Stores command-line arguments |
| `atoi()` | Converts text to an integer |
| `malloc()` | Dynamically allocates memory |
| `NULL` | Indicates allocation failure |
| Virtual Memory | Memory addresses used by a process |
| Physical RAM | Actual memory hardware |
| Page | Fixed-size block of memory |
| `4096` bytes | Common page size |
| `free()` | Releases dynamically allocated memory |
| `SIGINT` | Signal generated by Ctrl+C |
| `free -h` | Displays memory information |
| `watch` | Repeatedly executes a command |

---

# 23. Important Commands

## Compile the Program

```bash
gcc memory.c -o memory
```

## Run the Program

```bash
./memory 500
```

## Check Memory Usage

```bash
free -h
```

## Continuously Monitor Memory

```bash
watch -n 1 free -h
```

## Stop the Program

```text
Ctrl+C
```

---

# 24. Conclusion

This experiment demonstrates the difference between **requesting virtual memory** and **actually using memory pages**.

The program:

- Accepts memory size as a command-line argument.
- Allocates memory using `malloc()`.
- Checks whether allocation succeeds.
- Touches memory pages using a 4096-byte interval.
- Keeps the allocated memory active.
- Allows memory usage to be monitored using `free -h`.
- Demonstrates releasing memory when the program exits.

### Final Concept

```text
Request Memory
      ↓
Allocate Virtual Address Space
      ↓
Touch Memory Pages
      ↓
Physical RAM Backs Active Pages
      ↓
Monitor Using free -h
      ↓
Release Memory
```

**The main lesson is that requesting virtual memory and actually using physical memory are two different stages of memory management.**
