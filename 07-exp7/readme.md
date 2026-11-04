# Experiment 7: CPU-Bound and I/O-Bound C Programs Using `top`

## Aim

To write and run a CPU-bound C program and an I/O-bound C program, and compare their CPU utilization, process states, and accumulated CPU time using the `top` command.

---

## Program 1: I/O-Bound Program

### Filename: `io_bound.c`

```c
#include <stdio.h>
#include <unistd.h>

int main()
{
    FILE *fp;

    printf("Starting I/O task...\n");

    while (1)
    {
        fp = fopen("dummy.txt", "w");

        if (fp != NULL)
        {
            fprintf(fp, "Writing disk data\n");
            fclose(fp);

            printf("File operation done\n");
        }

        sleep(1);
    }

    return 0;
}
```

---

## Program 2: CPU-Bound Program

### Filename: `cpu_bound.c`

```c
#include <stdio.h>

int main()
{
    long long counter = 0;

    while (1)
    {
        counter++;

        if (counter % 100000000 == 0)
        {
            printf("CPU task running: %lld\n", counter);
        }
    }

    return 0;
}
```

---

# Compilation

Compile both programs using:

```bash
gcc io_bound.c -o io_bound
gcc cpu_bound.c -o cpu_bound
```

---

# Running the Programs

Run both programs in the background:

```bash
./io_bound &
```

```bash
./cpu_bound &
```

The `&` symbol runs the program in the background.

---

# Finding Process IDs

Use the following command:

```bash
pgrep -af 'io_bound|cpu_bound'
```

Example output:

```text
2451 ./io_bound
2452 ./cpu_bound
```

Your process IDs may be different.

---

# Using the `top` Command

To monitor all processes:

```bash
top
```

Press `P` to sort processes according to CPU utilization.

To monitor only the two programs:

```bash
top -p 2451,2452
```

Replace `2451` and `2452` with your actual process IDs.

To refresh every second:

```bash
top -d 1
```

To collect five snapshots:

```bash
top -b -n 5 -d 1
```

To view individual CPU cores, run:

```bash
top
```

Then press:

```text
1
```

---

# Expected Output

## I/O-Bound Program

```text
Starting I/O task...
File operation done
File operation done
File operation done
...
```

The program performs a file operation and then sleeps for one second.

---

## CPU-Bound Program

```text
CPU task running: 100000000
CPU task running: 200000000
CPU task running: 300000000
...
```

The program continuously performs calculations by incrementing the counter.

---

# Observation

| Parameter | CPU-Bound Program | I/O-Bound Program |
|---|---|---|
| CPU Utilization (%CPU) | Generally High | Generally Low |
| Process State | Usually `R` (Running) | Often `S` (Sleeping) |
| CPU Time (`TIME+`) | Increases Rapidly | Increases Slowly |
| Main Activity | Continuous Calculations | File Operations and Sleeping |

---

# Explanation

A **CPU-bound process** spends most of its time performing calculations. Therefore, it generally consumes a high percentage of CPU resources.

An **I/O-bound process** spends much of its time waiting for input/output operations or sleeping. Therefore, it generally uses less CPU.

The `top` command can be used to observe:

- CPU utilization
- Process state
- CPU time
- Running processes

---

# Result

Both the CPU-bound and I/O-bound C programs were executed successfully.

Using the `top` command, their CPU utilization, process states, and accumulated CPU time were observed. The CPU-bound program generally consumed more CPU resources and accumulated CPU time faster, while the I/O-bound program used less CPU because it spent much of its time sleeping after performing file operations.
