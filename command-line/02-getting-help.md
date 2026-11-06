# Getting Help in cmd.exe

> Windows command-line notes. Part of an ongoing command-line reference series.

With the basics of the Command Prompt behind us, the next skill is finding our way around when we don't remember a command or its exact syntax. CMD ships with a built-in help system that works even with no internet access, and there are a few navigation tricks that make long sessions a lot less painful.

Here's what we're covering in this file.

- Using the built-in `help` command
- Getting help for one specific command
- Why an offline help system still matters
- A couple of external references worth bookmarking
- Everyday tips, like clearing the screen, working with command history, and stopping a process

## Using the `help` Command

Staring at a blank prompt, two questions usually come up first. What commands can we run, and how do we use them?

Typing `help` by itself answers the first one. It prints a list of the built-in commands with a one-line description of each.

```powershell
C:\> help

For more information on a specific command, type HELP command-name
ASSOC          Displays or modifies file extension associations.
ATTRIB         Displays or changes file attributes.
BCDEDIT        Sets properties in boot database to control boot loading.
CD             Displays the name of or changes the current directory.
CHKDSK         Checks a disk and displays a status report.
CLS            Clears the screen.
...
```

Reading through that list is a quick way to spot the built-in that fits whatever we're trying to do.

### Help for a Specific Command

To get detail on one command, we run `help <command>`. Here's an example.

```powershell
C:\> help time

Displays or sets the system time.

TIME [/T | time]

Type TIME with no parameters to display the current time setting and a prompt
for a new one. Press ENTER to keep the same time.
```

That works for the built-in commands, though not for every executable on the system. Some commands have no page in the `help` utility and will point us to their own help switch instead.

```powershell
C:\> help ipconfig

This command is not supported by the help utility. Try "ipconfig /?".
```

That leads us to the second way to get help, the `/?` switch.

### The `/?` Switch

A lot of commands expose their own help when we tack on `/?`. For anything the `help` utility doesn't cover, this is usually the way in.

```powershell
C:\> ipconfig /?
```

Good habit to build. When `help <command>` comes up empty, try `<command> /?` instead.

## Why an Offline Help System Still Matters

It's fair to ask why a built-in help system matters at all when a web search is one tab away. The honest answer is that the internet isn't always reachable.

Picture an authorized internal assessment where we get dropped onto a Windows host, our outside devices have been collected, and the firewall blocks all outbound traffic. We're mid-enumeration and suddenly blank on the syntax for a command we need. With no route out to the internet, the built-in help is the only reference we've got.

That scenario is deliberately strict, but restricted, monitored, or air-gapped environments are very real. None of us memorize every command, every switch, and every bit of syntax, so having an offline manual on the box itself bridges that gap. If you've worked through the Linux side of things, the CMD help utility plays a role a lot like the `man` pages, a local no-network manual for the system's commands. That's not a knock on the internet. When we have unrestricted access, we should use it fully. The point is simply to have a fallback for when we don't.

## External References

When we do have internet access, these are solid references for command-line work, and they're worth keeping handy.

| Resource | What it's good for |
|---|---|
| Microsoft Learn (official docs) | A complete command listing with detailed usage. The online equivalent of the `man` pages. |
| ss64.com | A fast quick-reference for CMD, PowerShell, Bash, and more. |

That's a starting baseline rather than a full list, but it covers a lot of ground.

## Everyday Tips and Tricks

### Clear the Screen with `cls`

After a few commands, output piles up and the screen gets hard to read. `cls` wipes the terminal and gives us a clean prompt, which makes it much easier to see where the new output begins.

```powershell
C:\> cls
```

### Command History

Clearing the screen doesn't erase our history for the session. CMD keeps a running record of the commands we've entered in the current session, and there are several ways to get back to them.

We can see the full session history with `doskey /history`.

```powershell
C:\> doskey /history

cd C:\Users\student
dir /A
hostname
whoami
doskey /history
```

`doskey` is the MS-DOS utility that tracks the commands we've issued and lets us recall them. Here are the keys and commands worth knowing for working with history.

| Key / Command | What it does |
|---|---|
| `doskey /history` | Prints the session history, and it can be redirected to a file. |
| Up arrow | Scrolls back through previous commands. |
| Down arrow | Scrolls forward toward the most recent commands. |
| Right arrow | Retypes the previous command one character at a time. |
| Page Up | Loads the first command in the session history. |
| Page Down | Loads the last command in the session history. |
| F3 | Retypes the entire previous command. |
| F5 | Press it repeatedly to cycle back through previous commands. |
| F7 | Opens an interactive popup list of previous commands. |
| F9 | Recalls a command by its history number. |

> [!IMPORTANT]
> Unlike Bash and a lot of other shells, CMD doesn't hold onto history across sessions. Once we close the window, that session's history is gone.

To keep a copy, we redirect it to a file while the session is still open.

```powershell
C:\> doskey /history > command-log.txt
```

### Stop a Running Process with `Ctrl + C`

Sometimes we need to interrupt a command that's still running, either because we already have what we need or because it's hung on us. `Ctrl + C` sends an interrupt that stops the current process.

```powershell
C:\> ping 8.8.8.8

Pinging 8.8.8.8 with 32 bytes of data:
Reply from 8.8.8.8: bytes=32 time=22ms TTL=114
Reply from 8.8.8.8: bytes=32 time=25ms TTL=114
^C
```

> [!WARNING]
> Whatever was running gets cut off right away and might not finish cleanly, so it's worth being aware of what we're interrupting before we do it.

## Quick Reference

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
| Persistent history across sessions? | No, CMD clears it on close |

---

*Study notes for personal and classroom use. Techniques described here are for authorized systems only.*
