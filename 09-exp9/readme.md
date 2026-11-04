# Experiment 9: Process Creation and Termination Using C

## Aim

To write and execute C programs that demonstrate Process ID (PID), process creation using `fork()`, and parent-child synchronization using `wait()` under Linux.

---

## Program 1: Getting the Process ID (PID)

### Source Code

```c
#include <stdio.h>
#include <unistd.h>

int main()
{
    printf("I am a simple process. My PID is: %d\n", getpid());

    return 0;
}
```

### Compilation and Execution

```bash
gcc step1_pid.c -o step1_pid
./step1_pid
```

### Sample Output

```text
I am a simple process. My PID is: 3412
```

> **Note:** The PID will be different on your system.

---

## Program 2: Creating a Child Process Using `fork()`

### Source Code

```c
#include <stdio.h>
#include <unistd.h>

int main()
{
    pid_t p = fork();

    if (p == 0)
    {
        printf("I am the CHILD process! My own PID: %d\n", getpid());
    }
    else
    {
        printf("I am the PARENT process! fork() gave me child's PID: %d\n", p);
    }

    return 0;
}
```

### Compilation and Execution

```bash
gcc step2_fork.c -o step2_fork
./step2_fork
```

### Sample Output

```text
I am the PARENT process! fork() gave me child's PID: 3450
I am the CHILD process! My own PID: 3450
```

> **Note:** The order of the output may change because the parent and child processes run independently.

---

## Program 3: Process Creation and Synchronization Using `wait()`

### Source Code

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>

int main()
{
    pid_t p = fork();

    if (p == 0)
    {
        printf("[Child] Work started. My PID: %d\n", getpid());

        sleep(2);

        printf("[Child] Work finished! Exiting now.\n");

        exit(0);
    }
    else
    {
        printf("[Parent] Child started. Now waiting for it to finish...\n");

        pid_t terminated_pid = wait(NULL);

        printf("[Parent] Child with PID %d has terminated!\n", terminated_pid);
    }

    return 0;
}
```

### Compilation and Execution

```bash
gcc process_creation_wait.c -o exp9
./exp9
```

### Sample Output

```text
[Parent] Child started. Now waiting for it to finish...
[Child] Work started. My PID: 3450
[Child] Work finished! Exiting now.
[Parent] Child with PID 3450 has terminated!
```

> **Note:** The PID values will be different on every system.

---

# Explanation

## 1. Process ID (PID)

A **Process ID (PID)** is a unique number assigned by the operating system to every running process.

The function:

```c
getpid()
```

returns the PID of the currently running process.

---

## 2. `fork()`

The `fork()` function creates a new process called the **child process**.

After `fork()`:

| Process | Return Value |
|---|---|
| Child Process | `0` |
| Parent Process | PID of the child |

```text
        Original Process
               |
             fork()
            /      \
           /        \
      Parent        Child
     p > 0          p = 0
```

---

## 3. `wait(NULL)`

The `wait(NULL)` function makes the parent process wait until its child process terminates.

```c
pid_t terminated_pid = wait(NULL);
```

It returns the PID of the child process that has terminated.

---

## Observation Table

| Execution Step | Process | Observation |
|---|---|---|
| `getpid()` | Single Process | Displays current process PID |
| `fork()` | Parent | Receives child PID |
| `fork()` | Child | Receives `0` |
| `wait(NULL)` | Parent | Waits for child termination |

---

## Result

The C programs were successfully executed to demonstrate **Process ID retrieval using `getpid()`**, **process creation using `fork()`**, and **parent-child synchronization using `wait()`**. The experiment showed how Linux creates and manages processes and coordinates the termination of child processes.
