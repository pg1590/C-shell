# Unix Shell (C, Linux, Systems Programming)

A custom Unix-like shell developed in C that replicates core functionalities of standard Linux shells while providing several custom utilities for process management, file exploration, and system monitoring.

The project explores fundamental Operating Systems concepts such as process creation, signal handling, inter-process communication, terminal control, filesystem traversal, and command execution.

---

## Features

### Shell Core

- Execute standard Linux system commands.
- Support for multiple commands in a single input using `;`.
- Background execution using `&`.
- Dynamic shell prompt displaying:
  - Username
  - System hostname
  - Current working directory.
- Relative path handling similar to conventional Unix shells.

---

### Command Parsing

- Robust tokenization and parsing logic.
- Handles:
  - Multiple spaces and tabs.
  - Quoted strings.
  - Multiple commands in a single line.
- Supports combinations of:
  - Background processes
  - Pipes
  - Redirection

---

### Custom Commands

#### hop

Custom directory navigation command.

Supports:

```bash
hop .
hop ..
hop ~
hop <directory>
```

Allows sequential navigation through multiple directories.

---

#### reveal

Custom file listing utility similar to `ls`.

Supports:

```bash
reveal
reveal -a
reveal -l
reveal -la
```

Features:

- Hidden file listing (`-a`)
- Detailed file information (`-l`)
- Multiple occurrences of flags handled gracefully

Examples:

```bash
reveal
reveal -aaa
reveal -l
reveal -la
reveal -alll
```

Behavior:

| Command | Equivalent |
|----------|------------|
| reveal | reveal -a |
| reveal -aaa | reveal -a |
| reveal -laa | reveal -l |
| reveal -alll | reveal -l |

---

#### log

Command history management utility.

Features:

- Stores last 15 commands.
- Displays command history.
- Clears history.
- Re-executes previous commands.

---

#### proclore

Displays information about a process.

Usage:

```bash
proclore
proclore <pid>
```

Information displayed:

- Process status
- Process group
- Memory usage
- Executable path

Uses the Linux `/proc` filesystem.

---

#### seek

Recursive file and directory search utility.

Usage:

```bash
seek <flags> <search_term> <directory>
```

Features:

- Search files
- Search directories
- Permission checks
- Search with or without extensions

Examples:

```bash
seek main .
seek -f main .
seek -d src .
```

---

#### activities

Displays all processes spawned by the shell.

Information shown:

- Command name
- PID
- Process state

Output is maintained in lexicographic order.

Example:

```bash
activities
```

Sample output:

```text
sleep : 12345 : Running
vim   : 12350 : Stopped
```

---

#### ping

Sends signals to processes.

Usage:

```bash
ping <pid> <signal_number>
```

Example:

```bash
ping 12345 9
```

Supported signal numbers:

```text
0 - 31
```

---

#### fg

Brings a background process to the foreground.

Usage:

```bash
fg <pid>
```

---

#### bg

Resumes a stopped process in the background.

Usage:

```bash
bg <pid>
```

---

#### neonate

Monitors newly created processes in the system.

Usage:

```bash
neonate -n <time_interval>
```

Example:

```bash
neonate -n 3
```

Uses:

- `/proc`
- `readdir()`
- `select()`

to periodically display newly created process IDs.

---

#### iMan

Fetches Linux man pages from the internet using sockets.

Usage:

```bash
iMan <command>
```

Example:

```bash
iMan grep
```

Retrieves documentation from:

http://man.he.net

and prints the contents directly to the terminal.

---

## I/O Redirection

Supports standard Unix redirection operators.

### Output Redirection

```bash
command > output.txt
```

### Append Redirection

```bash
command >> output.txt
```

### Input Redirection

```bash
command < input.txt
```

### Combined Redirection

```bash
sort < input.txt > output.txt
```

---

## Pipes

Supports arbitrary-length pipelines.

Examples:

```bash
cat file.txt | grep error
```

```bash
cat file.txt | grep error | sort
```

```bash
cat file.txt | grep error | sort | uniq
```

Pipelines are executed from left to right using process chaining and inter-process communication.

---

## Pipes with Redirection

Supports combinations of pipes and redirection.

Examples:

```bash
cat file.txt | grep error > output.txt
```

```bash
sort < input.txt | uniq > output.txt
```

```bash
cat log.txt | grep warning | sort > warnings.txt
```

---

## Job Control

Supports:

- Foreground processes
- Background processes
- Process suspension
- Process continuation

Example:

```bash
sleep 60 &
```

Output:

```text
[1] 12345
```

---

## Signal Handling

The shell correctly handles common terminal signals.

### Ctrl + C (SIGINT)

- Terminates foreground process.
- Shell remains active.

### Ctrl + Z (SIGTSTP)

- Stops foreground process.
- Moves process to background job list.

### Ctrl + D (EOF)

- Gracefully exits the shell.
- Terminates active child processes.

---

## myshrc Configuration

Supports shell customization through a `.myshrc` file.

### Aliases

Example:

```bash
alias ll="reveal -la"
```

### Functions

Example:

```bash
mk_hop()
{
    mkdir $1
    hop $1
}
```

Example:

```bash
hop_seek()
{
    hop $1
    seek $2 .
}
```

---

## Operating Systems Concepts Used

This project extensively uses:

- Process creation using `fork()`
- Program execution using `execvp()`
- Process synchronization using `wait()` and `waitpid()`
- Signal handling using `signal()` and `kill()`
- Inter-process communication using `pipe()`
- Filesystem traversal using `opendir()` and `readdir()`
- Process introspection through `/proc`
- Terminal management using `tcgetattr()` and `tcsetattr()`
- Socket programming for online man-page retrieval

---

## Project Structure

```text
.
├── src/
│   ├── shell.c
│   ├── parser.c
│   ├── hop.c
│   ├── reveal.c
│   ├── seek.c
│   ├── proclore.c
│   ├── activities.c
│   ├── neonate.c
│   ├── ping.c
│   ├── iman.c
│   ├── redirection.c
│   ├── pipes.c
│   └── signals.c
│
├── include/
│   └── *.h
│
├── myshrc
│
└── README.md
```

---

## Building

Compile using GCC:

```bash
gcc *.c -o mysh
```

Or using Make:

```bash
make
```

---

## Running

```bash
./mysh
```

---

## Example Session

```bash
user@system:~> reveal -la

user@system:~> seek main .

user@system:~> sleep 30 &

[1] 12453

user@system:~> activities

sleep : 12453 : Running

user@system:~> cat file.txt | grep error | sort > output.txt

user@system:~> proclore 12453
```

---

## Technical Highlights

- Written entirely in C.
- Modular architecture with separate source and header files.
- Linux process management and job control.
- Custom command implementation.
- Arbitrary-length pipeline support.
- Combined pipe and redirection support.
- Online man-page retrieval using sockets.
- Persistent command history management.
- Extensive error handling for robust shell execution.

---

## Future Improvements

- Tab completion
- Environment variable expansion
- Shell scripting support
- Command auto-suggestions
- Advanced job scheduler
- Command timing and profiling
- Plugin architecture for custom commands

---

## Author

**Prakhar Gupta**

B.Tech, Electronics and Communication Engineering

Indian Institute of Information Technology Design and Manufacturing (IIITDM) Kancheepuram
