# Experiment 8: Observing Process Memory Maps Using `pmap`

## Aim

To examine the memory mappings of the CPU-bound and I/O-bound programs from Experiment 7 using the `pmap` command.

---

# Programs Used

This experiment uses the following programs from **Experiment 7**:

- `cpu_bound`
- `io_bound`

No new C program is required for this experiment.

---

# Procedure

## Step 1: Run Both Programs

Run the I/O-bound program in the background:

```bash
./io_bound &
```

Run the CPU-bound program in the background:

```bash
./cpu_bound &
```

The `&` symbol runs the programs in the background.

---

## Step 2: Find the Process IDs (PIDs)

Use the following command:

```bash
pgrep -af 'io_bound|cpu_bound'
```

Example output:

```text
2451 ./io_bound
2452 ./cpu_bound
```

Your PID numbers may be different.

---

## Step 3: Display the CPU-Bound Process Memory Map

Use:

```bash
pmap <CPU_PID>
```

Example:

```bash
pmap 2452
```

For detailed memory information:

```bash
pmap -x <CPU_PID>
```

Example:

```bash
pmap -x 2452
```

---

## Step 4: Display the I/O-Bound Process Memory Map

Use:

```bash
pmap <IO_PID>
```

Example:

```bash
pmap 2451
```

For detailed memory information:

```bash
pmap -x <IO_PID>
```

Example:

```bash
pmap -x 2451
```

---

# Understanding `pmap -x`

The command:

```bash
pmap -x <PID>
```

displays detailed information about the memory regions of a process.

The output generally contains the following columns:

| Column | Meaning |
|---|---|
| Address | Starting address of the memory region |
| Kbytes | Total size of the memory region |
| RSS | Memory currently resident in physical RAM |
| Dirty | Memory pages that are modified |
| Mode | Memory permissions |
| Mapping | File, library, or memory region associated with the mapping |

---

# Memory Permissions

The `Mode` column shows what operations are allowed.

| Symbol | Meaning |
|---|---|
| `r` | Read permission |
| `w` | Write permission |
| `x` | Execute permission |
| `-` | Permission not given |

Examples:

```text
r-x
```

Read and execute permission. This is commonly used for program code.

```text
rw-
```

Read and write permission. This is commonly used for data and stack memory.

```text
r--
```

Read-only permission.

---

# Important Memory Regions

The memory map of both programs may contain the following regions:

| Memory Region | Meaning |
|---|---|
| Executable Code | Machine instructions generated from the C program |
| Data/BSS | Global or static data |
| Stack | Function calls and local variables |
| Heap | Dynamic memory allocation |
| Shared Libraries | Libraries such as `libc.so.6` |
| Other Mappings | Additional runtime and operating system memory regions |

Neither program explicitly uses `malloc()`, so a large heap allocation is not required for the main task.

---

# Example `pmap -x` Output

The following is only an example. Your addresses and values will be different.

```text
Address           Kbytes     RSS   Dirty Mode  Mapping
0000563c1a2b4000       4       4       0 r---- cpu_bound
0000563c1a2b5000       4       4       0 r-x-- cpu_bound
0000563c1a2b7000       4       4       4 rw--- cpu_bound
00007f8e3c000000    1892    1124       0 r-x-- libc.so.6
00007ffd5e2a1000     132      12      12 rw--- [ stack ]

total kB            2500    1400     120
```

---

# Explanation of the Columns

## 1. Address

The **Address** column shows where a memory region starts in the process's virtual address space.

It is a virtual address and is not necessarily a direct physical RAM address.

---

## 2. Kbytes

**Kbytes** shows the total size of a memory mapping.

A larger value does not necessarily mean all of that memory is currently present in physical RAM.

---

## 3. RSS

**RSS** stands for **Resident Set Size**.

It shows how much of the memory region is currently resident in physical RAM.

Generally:

```text
RSS ≤ Kbytes
```

---

## 4. Dirty

The **Dirty** column shows memory pages that are counted as modified.

Generally:

```text
Dirty ≤ RSS ≤ Kbytes
```

---

## 5. Mode

The **Mode** column displays the permissions of the memory region.

Examples:

```text
r-x--
```

Read and execute permissions.

```text
rw---
```

Read and write permissions.

---

## 6. Mapping

The **Mapping** column shows what the memory region belongs to.

Examples include:

```text
cpu_bound
io_bound
libc.so.6
ld-linux-x86-64.so.2
[ anon ]
[ stack ]
```

| Mapping | Meaning |
|---|---|
| `cpu_bound` | CPU-bound program executable |
| `io_bound` | I/O-bound program executable |
| `libc.so.6` | C standard library |
| `ld-linux-x86-64.so.2` | Dynamic linker/loader |
| `[ anon ]` | Anonymous memory |
| `[ stack ]` | Main thread stack |

---

# Observation Table

Record your actual readings from your system.

| Parameter | CPU-Bound Process | I/O-Bound Process |
|---|---|---|
| Executable Mapping | Present | Present |
| Shared Libraries | Typically Present | Typically Present |
| Stack | Typically Present | Typically Present |
| Total Mapped Memory | Record Actual Value | Record Actual Value |
| RSS | Record Actual Value | Record Actual Value |

---

# Analysis

The CPU-bound program continuously performs calculations, while the I/O-bound program performs file operations and sleeps.

These differences mainly affect:

- CPU utilization
- Process state
- CPU time

They do not necessarily cause a major difference in the overall memory map.

The `pmap` command is used to inspect the memory mappings of a process, while the `top` command is used to observe CPU utilization and process activity.

---

# Result

The memory maps of the CPU-bound and I/O-bound programs were successfully examined using `pmap` and `pmap -x`.

The executable mappings, shared libraries, stack regions, memory permissions, RSS, and other memory statistics were observed and compared.

---

# Cleanup

After completing the experiment, find the running processes:

```bash
pgrep -af 'io_bound|cpu_bound'
```

Terminate them using their actual PIDs:

```bash
kill <IO_PID> <CPU_PID>
```

Example:

```bash
kill 2451 2452
```

This stops the programs and prevents their infinite loops from continuing to run.
