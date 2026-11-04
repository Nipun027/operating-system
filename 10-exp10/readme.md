# Experiment 10: Observing Virtual Memory Addresses in C

## Aim

To write, compile, and execute a C program that prints the memory address of a local variable, run multiple concurrent instances of the program, and observe the effect of Address Space Layout Randomization (ASLR).

---

## Objectives

- To understand virtual memory addresses in C.
- To print the address of a local variable using a pointer.
- To run multiple processes simultaneously.
- To observe the effect of ASLR on memory addresses.
- To understand how the MMU and page tables provide process memory isolation.

---

# Program: Virtual Memory Address

## Filename: `virtual_addr.c`

```c
#include <stdio.h>
#include <unistd.h>

int main()
{
    int var = 42;

    printf("PID: %d | Address: %p\n", getpid(), (void *)&var);

    fflush(stdout);

    while (1)
    {
        sleep(1);
    }

    return 0;
}
```

---

## Compilation

Compile the program using GCC:

```bash
gcc virtual_addr.c -o virtual_addr
```

---

# Step 1: Run Multiple Instances with ASLR Active

Run four instances of the program in the background:

```bash
./virtual_addr &
./virtual_addr &
./virtual_addr &
./virtual_addr &
```

### Example Output

```text
PID: 5101 | Address: 0x7ffd8a3e41ac
PID: 5102 | Address: 0x7ffc1b9028dc
PID: 5103 | Address: 0x7ffe44d1953c
PID: 5104 | Address: 0x7ffdf012b76c
```

> The PID and memory addresses will be different on your system.

With ASLR enabled, different executions of the same program will generally display different virtual memory addresses.

---

# Step 2: Verify Running Processes

Use the following command:

```bash
pgrep -a virtual_addr
```

### Example Output

```text
5101 ./virtual_addr
5102 ./virtual_addr
5103 ./virtual_addr
5104 ./virtual_addr
```

This confirms that multiple independent instances of the program are running simultaneously.

---

# Step 3: Terminate the Processes

After completing the observation, stop the running processes:

```bash
killall virtual_addr
```

---

# Step 4: Run with ASLR Disabled

Compile the program without PIE:

```bash
gcc -no-pie virtual_addr.c -o virtual_addr_fixed
```

Run multiple instances with ASLR disabled:

```bash
setarch $(uname -m) -R ./virtual_addr_fixed &
setarch $(uname -m) -R ./virtual_addr_fixed &
setarch $(uname -m) -R ./virtual_addr_fixed &
setarch $(uname -m) -R ./virtual_addr_fixed &
```

### Example Output

```text
PID: 5210 | Address: 0x7fffffffde4c
PID: 5211 | Address: 0x7fffffffde4c
PID: 5212 | Address: 0x7fffffffde4c
PID: 5213 | Address: 0x7fffffffde4c
```

When ASLR is disabled, the stack layout can become predictable, so identical program instances under the same conditions may display the same virtual address.

---

# Step 5: Final Cleanup

Terminate all running instances:

```bash
killall virtual_addr_fixed
```

---

# Line-by-Line Explanation

## `#include <stdio.h>`

Provides functions such as:

- `printf()`
- `fflush()`

---

## `#include <unistd.h>`

Provides Linux/Unix functions such as:

- `getpid()`
- `sleep()`

---

## `int var = 42;`

Creates a local integer variable named `var`.

Local variables are normally stored in the process's stack memory.

---

## `&var`

The `&` operator gives the memory address of the variable.

```c
&var
```

This address is a **virtual memory address**, not a physical RAM address.

---

## `(void *)&var`

The `%p` format specifier expects a pointer value.

Therefore, the address is cast to `void *`:

```c
(void *)&var
```

---

## `getpid()`

Returns the Process ID (PID) of the currently running process.

---

## `printf()`

```c
printf("PID: %d | Address: %p\n", getpid(), (void *)&var);
```

Displays:

- The Process ID.
- The virtual address of the variable.

---

## `fflush(stdout)`

Forces the output to be displayed immediately.

This ensures that the PID and address appear before the program enters the infinite loop.

---

## `while (1)`

Creates an infinite loop:

```c
while (1)
{
    sleep(1);
}
```

This keeps the process running so it can be observed using Linux commands.

---

## `sleep(1)`

Pauses the process for approximately one second.

This prevents the program from continuously using 100% of a CPU core.

---

# Virtual Address vs Physical Address

A C pointer stores a **virtual memory address**.

The program does not directly access physical RAM.

```text
C Program
    |
    v
Virtual Address
    |
    v
MMU
    |
    v
Page Table
    |
    v
Physical RAM
```

The CPU's **Memory Management Unit (MMU)** translates virtual addresses into physical memory addresses.

---

# Address Space Layout Randomization (ASLR)

ASLR is a security feature used by modern operating systems.

It randomizes memory locations such as:

- Stack
- Heap
- Shared libraries

With ASLR enabled:

```text
Process 1 → Different address
Process 2 → Different address
Process 3 → Different address
Process 4 → Different address
```

This makes memory addresses more difficult to predict.

---

# Why Processes Do Not Collide

Different processes can use the same virtual address without interfering with each other.

For example:

```text
Process 1
Virtual Address: 0x7fffffffde4c
        |
        v
Page Table 1
        |
        v
Physical Memory Frame A
```

```text
Process 2
Virtual Address: 0x7fffffffde4c
        |
        v
Page Table 2
        |
        v
Physical Memory Frame B
```

Although both processes use the same virtual address, their page tables can map that address differently.

This provides memory isolation.

---

# Observation Table

| Instance | ASLR Mode | Process | Printed Virtual Address | Memory Collision |
|---|---|---|---|---|
| Process 1 | Active | Record PID | Usually different | None |
| Process 2 | Active | Record PID | Usually different | None |
| Process 3 | Active | Record PID | Usually different | None |
| Process 4 | Active | Record PID | Usually different | None |
| Process 1 | Disabled | Record PID | May be identical under the same conditions | None |
| Process 2 | Disabled | Record PID | May be identical under the same conditions | None |
| Process 3 | Disabled | Record PID | May be identical under the same conditions | None |
| Process 4 | Disabled | Record PID | May be identical under the same conditions | None |

---

# Quick Summary

- C pointers represent **virtual addresses**.
- Every process has its own virtual address space.
- `getpid()` displays the Process ID.
- `&var` gives the address of a variable.
- ASLR randomizes memory locations for security.
- The MMU translates virtual addresses to physical memory.
- Page tables provide memory isolation between processes.

---

# Result

The program `virtual_addr.c` was successfully compiled and executed using multiple concurrent processes. The experiment demonstrated that memory addresses printed by C programs are virtual addresses rather than direct physical RAM addresses. With ASLR enabled, different executions generally displayed different memory locations. The experiment also demonstrated how the operating system and the Memory Management Unit (MMU) use separate page tables to provide memory isolation between concurrent processes.
