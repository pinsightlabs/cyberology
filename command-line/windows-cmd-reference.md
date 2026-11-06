# Windows Command Line Reference

A single-page reference for the Windows Command Prompt (`cmd.exe`), the command line built into every version of Windows. We type a command, Windows runs it, and we get a result back. This page covers the basics, getting help, moving around, working with files, and pulling system information. Use `Ctrl + F` to jump to a command.

## Opening cmd

- `Windows + R`, type `cmd`, press Enter
- Or search "cmd" in the Start menu
- Lives at `C:\Windows\System32\cmd.exe`

## Basics

| Command | What it does |
|---|---|
| `dir` | List files and folders in the current directory |
| `cls` | Clear the screen |
| `echo <text>` | Print text to the screen |
| `ver` | Show the Windows version |
| `hostname` | Show the computer's name |
| `whoami` | Show the current user |
| `exit` | Close the Command Prompt |

Reading a `dir` listing: `<DIR>` means a folder, anything without it is a file. `.` is the current folder, `..` is the one above it.

## Getting Help

| Command | What it does |
|---|---|
| `help` | List the built-in commands |
| `help <command>` | Show help for one command |
| `<command> /?` | Another way to get help for a command |
| `doskey /history` | Show the commands run this session |
| Up arrow / `F3` | Recall the previous command |
| `F7` | Pop-up list of previous commands |
| `Ctrl + C` | Stop a running command |

Note: cmd does not keep command history after you close the window.

## Navigation

| Command | What it does |
|---|---|
| `cd` | Show the current directory |
| `cd <path>` | Change to another directory |
| `cd ..` | Move up one folder |
| `cd \` | Jump to the drive root |
| `tree` | Show the folder structure |
| `tree /F` | Show folders and files |

Absolute path starts at the root (`C:\Users\student`). Relative path starts where you are (`.\Pictures`).

## Files and Folders

| Command | What it does |
|---|---|
| `md` or `mkdir <name>` | Make a folder |
| `rd` or `rmdir <name>` | Remove an empty folder |
| `rd /S <name>` | Remove a folder and everything in it |
| `type <file>` | Show a file's contents |
| `more <file>` | Show a file one screen at a time |
| `ren <old> <new>` | Rename a file |
| `copy <src> <dest>` | Copy a file |
| `move <src> <dest>` | Move a file |
| `del` or `erase <file>` | Delete a file |
| `del /F <file>` | Force-delete a read-only file |
| `robocopy <src> <dest>` | Copy folders, with more control |

## Redirection and Chaining

| Operator | What it does |
|---|---|
| `>` | Send output to a file (overwrites) |
| `>>` | Add output to the end of a file |
| `<` | Feed a file into a command |
| `\|` | Send one command's output into another |
| `&` | Run B after A, always |
| `&&` | Run B only if A succeeds |
| `\|\|` | Run B only if A fails |

Example: `ipconfig /all | find "IPv4"` runs `ipconfig /all` and shows only the lines with "IPv4".

## System Information

| Command | What it does |
|---|---|
| `systeminfo` | Full host summary (OS, build, hotfixes) |
| `ipconfig` | Basic network config |
| `ipconfig /all` | Full network config (MAC, DNS, DHCP) |
| `arp /a` | Hosts seen in the ARP cache |
| `whoami /priv` | Current user's privileges |
| `whoami /groups` | Current user's groups |
| `whoami /all` | User, privileges, and groups |
| `net user` | List local accounts |
| `net localgroup` | List local groups |
| `net share` | Shared resources on the host |
| `net view` | Shares other hosts advertise |

## Sticky Keys (Physical Access Note)

Recovery media drops to a Command Prompt before Windows protections load. Swapping `sethc.exe` for `cmd.exe` and pressing Shift 5 times at login gives a SYSTEM shell with no login. Defense: BitLocker, firmware password, and blocking boot from external media.

---

*Study notes for authorized systems only.*
