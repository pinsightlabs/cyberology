# Command Prompt (cmd.exe) Basics

> Windows command-line notes. Part of an ongoing command-line reference series.

## What cmd.exe Is

The Command Prompt (`cmd.exe`, often just called CMD) is the default command-line interpreter that ships with Windows. It traces back to `COMMAND.COM` from the MS-DOS era and has carried forward into basically every modern Windows release, which is exactly why it's worth our time. It's everywhere.

A command-line interpreter takes the text commands we type, hands them to the operating system, and returns the result. One short line can do work that would take several clicks in a graphical tool, and since there's no GUI to draw, it leans on far fewer system resources (less CPU and memory).

PowerShell gets most of the attention these days, but CMD still earns its keep for a few practical reasons.

- It's present on legacy and minimal Windows installs where PowerShell might be missing or stripped down.
- Administrators sometimes lock down or block PowerShell with application control like AppLocker while leaving CMD reachable.
- Plenty of older software and scripts still assume a CMD environment.

The takeaway for us is to treat CMD as a baseline skill. When PowerShell isn't available or it's been restricted, the Command Prompt is often still a usable path for troubleshooting, administration, or, in an authorized assessment, working toward further access.

## How to Access cmd.exe

There isn't one single right way to open CMD. Which method we reach for depends on where we are and what access we already have. Before we list the methods, it helps to separate two kinds of access.

### Local Access vs. Remote Access

Local access means we're sitting at the machine itself, or at its console if it's a virtual machine. We reach it directly through the physical peripherals, the keyboard, monitor, and mouse. No network connection is required, since we're interacting with the box in front of us.

Here are the common ways to open CMD locally.

- Press `Windows key + R` to open the Run dialog, type `cmd`, and press Enter.
- Search for "cmd" or "Command Prompt" in the Start menu.
- Launch the executable straight from its path, `C:\Windows\System32\cmd.exe`.

Once it opens, we land at a prompt that shows our current location.

```powershell
Microsoft Windows [Version 10.0.19045.xxxx]
(c) Microsoft Corporation. All rights reserved.

C:\Users\student>
```

From here we can run commands, scripts, and whatever else we need.

Remote access means we're reaching the machine over the network instead of standing in front of it. We don't need physical access, but we do need a network route to the target and a working remote protocol. The common options are listed below.

| Protocol / Tool | Notes |
|---|---|
| Telnet | Legacy and unencrypted. Avoid it. |
| SSH | Encrypted remote shell. The preferred option for secure access. |
| RDP | Remote Desktop, a full graphical session. |
| WinRM | Windows Remote Management, used for remote command execution. |
| PsExec | A Sysinternals tool for running commands on remote hosts. |

Remote access is a huge convenience for administrators, since nobody wants to walk to every desk. It's also a real risk surface. When remote tools are misconfigured, or an attacker gets hold of valid credentials, that same convenience hands wide reach across the environment. As defenders, the balancing act is keeping systems available to the people who need them while protecting integrity and limiting exposure.

## Walking the Shell

Navigating the Command Prompt has barely changed in decades. Here's a mental model that helps. Moving through the file system is a lot like walking a hallway lined with doors. Each directory is a stretch of hallway. We step into one, look around to see what's there, then decide where to go next.

The core loop is a simple back-and-forth. We type a command, the system runs it and prints the result, and then we're back at the prompt ready for the next one.

### The `dir` Command

`dir` lists the contents of the current directory. It's usually the first thing we run to get our bearings.

```powershell
C:\Users\student\Desktop> dir

 Volume in drive C has no label.
 Volume Serial Number is XXXX-XXXX

 Directory of C:\Users\student\Desktop

11/05/2026  09:14 PM    <DIR>          .
11/05/2026  09:14 PM    <DIR>          ..
11/05/2026  09:10 PM                 0 notes.txt
11/05/2026  09:10 PM             1,204 report.docx
11/05/2026  09:12 PM    <DIR>          scripts
               2 File(s)          1,204 bytes
               3 Dir(s)  40,000,000,000 bytes free
```

Here's how to read that output.

- `C:\Users\student\Desktop>` is our current path, the "where am I" part of the prompt.
- `dir` is the command we issued.
- Everything below the command line is the system's response.
- `<DIR>` marks a directory (a folder). Entries without it are files.
- `.` is the current directory and `..` is the parent directory.
- The footer sums up the file count, the total bytes used, and the free space on the drive.

That's the whole pattern in miniature. We asked for a listing of the working directory, and the shell answered.

## Security Note: Command Prompt via Windows Recovery

When a machine won't boot normally, or a user is locked out, booting from Windows installation media offers a Repair or Recovery option that can drop us to a Command Prompt. That gives us a command line for offline troubleshooting before the normal OS and its protections are fully loaded.

That capability is genuinely useful for legitimate recovery, and it's also a classic physical-access attack path that's worth understanding as defenders.

Here's the technique in question. From a recovery prompt, an attacker with physical access to an older or unhardened system can replace the Sticky Keys binary (`sethc.exe`) with a copy of `cmd.exe`. After a reboot, pressing `Shift` five times at the login screen should trigger Sticky Keys. Instead it launches a Command Prompt, and that prompt runs with `NT AUTHORITY\SYSTEM` privileges. The result is a shell with full system rights before anyone has logged in, which bypasses authentication entirely.

> [!CAUTION]
> Only ever use a technique like this on a system you own or have explicit written authorization to test.

Why does this matter for our defense? A few reasons stand out.

- Physical access is a serious trust boundary. Once someone can boot their own media and touch the file system, a lot of our software protections no longer apply.
- Mitigations include full-disk encryption like BitLocker, firmware and BIOS passwords, disabling boot from external media, and restricting physical access to the machines themselves.
- This is exactly the kind of technique we only ever use on systems we own or have explicit written authorization to test.

## Quick Reference

| Item | Value |
|---|---|
| Executable | `cmd.exe` |
| Default path | `C:\Windows\System32\cmd.exe` |
| Open via Run | `Windows + R`, type `cmd` |
| List directory contents | `dir` |
| Directory marker in `dir` | `<DIR>` |
| Current directory | `.` |
| Parent directory | `..` |
| Recovery prompt risk | `sethc.exe` swapped for `cmd.exe` (Sticky Keys bypass) |
| Secure remote shell | SSH |
| Insecure remote shell (avoid) | Telnet |

---

*Study notes for personal and classroom use. Techniques described here are for authorized systems only.*
