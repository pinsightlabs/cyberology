# cmd.exe Quick Reference

> Every command from the Command Line section on one page, grouped by topic. For authorized systems and study use only.

> [!TIP]
> Keep this page open in a second tab while you work through the notes or practice in a lab.

## Opening and Reading the Shell

| Task | Command / Key |
|---|---|
| Executable | `cmd.exe` |
| Default path | `C:\Windows\System32\cmd.exe` |
| Open via Run | `Windows + R`, type `cmd` |
| Current directory marker | `.` |
| Parent directory marker | `..` |
| Directory marker in `dir` output | `<DIR>` |

## Getting Help

| Task | Command / Key |
|---|---|
| List all built-in commands | `help` |
| Help for one command | `help <command>` |
| Alternate help for a command | `<command> /?` |
| Clear the screen | `cls` |
| Show session command history | `doskey /history` |
| Save history to a file | `doskey /history > file.txt` |
| Recall previous command | Up arrow or `F3` |
| Interactive history popup | `F7` |
| Interrupt a running process | `Ctrl + C` |

## Navigating the File System

| Task | Command |
|---|---|
| List current directory | `dir` |
| Full `dir` options | `dir /?` |
| Show current working directory | `cd` or `chdir` |
| Move to an absolute path | `cd C:\full\path` |
| Move to a relative path | `cd .\folder` |
| Move up one level | `cd ..` |
| Move up three levels | `cd ..\..\..\` |
| Directory tree (folders only) | `tree` |
| Directory tree with files | `tree /F` |

## Working with Directories

| Task | Command |
|---|---|
| Create a directory | `md` or `mkdir` |
| Delete an empty directory | `rd` or `rmdir` |
| Delete a directory and contents | `rd /S <dir>` |
| Move a directory | `move <src> <dest>` |
| Copy with attributes (legacy) | `xcopy <src> <dest> /E` |
| Robust copy / sync | `robocopy <src> <dest>` |
| Preview a robocopy run | `robocopy ... /L` |

## Working with Files

| Task | Command |
|---|---|
| View a file one screen at a time | `more <file>` |
| Display file contents | `type <file>` |
| See open files (admin) | `openfiles` |
| Create or overwrite a file | `echo text > file` |
| Append to a file | `echo text >> file` |
| Create a sized file | `fsutil file createNew <name> <size>` |
| Rename a file | `ren` or `rename` |
| Copy a file (with validation) | `copy <src> <dest> /V` |
| Move a file | `move <src> <dest>` |
| Delete a file | `del` or `erase` |
| List files by attribute | `dir /A:R` or `dir /A:H` |
| Delete files by attribute | `del /A:R *` |
| Force delete | `del /F <file>` |

## Input, Output, and Chaining

| Operator | Meaning |
|---|---|
| `>` | Send output to a file (overwrites) |
| `>>` | Append output to a file |
| `<` | Feed a file into a command |
| `\|` | Pipe one command's output into another |
| `&` | Run B after A, always |
| `&&` | Run B only if A succeeds |
| `\|\|` | Run B only if A fails |

## Gathering System Information

| Task | Command |
|---|---|
| Full host summary | `systeminfo` |
| Hostname only | `hostname` |
| OS version only | `ver` |
| Basic network config | `ipconfig` |
| Full network config | `ipconfig /all` |
| Hosts seen in ARP cache | `arp /a` |
| Current user | `whoami` |
| Current user privileges | `whoami /priv` |
| Current user groups | `whoami /groups` |
| User, privileges, and groups | `whoami /all` |
| List local accounts | `net user` |
| Domain groups (on a DC) | `net group` |
| Local groups (any host) | `net localgroup` |
| Shared resources on the host | `net share` |
| Shares a host knows about | `net view` |

---

*Part of the Cyberology knowledge base, Command Line section. For authorized systems and classroom use only.*
