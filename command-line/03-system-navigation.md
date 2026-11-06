# Navigating the System with cmd.exe

> Windows command-line notes. Part of an ongoing command-line reference series.

Up to this point we've been getting comfortable with what the Command Prompt is and how to ask it for help. The natural next step is learning to move around. In this file we're going to work on owning our surroundings by listing a directory, figuring out where we are, moving with `cd`, and exploring the wider file system. At the end we'll look at a handful of directories that are worth knowing about, both from an attacker's point of view and from ours as defenders.

## Listing a Directory

The easiest thing we can do when we first land on a Windows host is see what's in the directory we're standing in. The `dir` command handles that for us.

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

`dir` is easy to use and more versatile than it first looks. Called with no arguments it hands us the contents of wherever we currently are. If we want to see everything it can do, we can fall back on `dir /?` from the Getting Help section and read through the full set of switches. For now the basic version gives us plenty to work with while we learn to move.

## Finding Our Place

Before we run anything on a host, it helps to know exactly where we are in the file system. The `cd` command (and its twin `chdir`) tells us that when we run it with no arguments.

```powershell
C:\Users\student\Desktop> cd

C:\Users\student\Desktop
```

That path it prints back is our current working directory. Think of it as our starting point. Any command we run from here without naming a different path is going to reference this spot. That matters more than it looks like at first, because almost everything we do from now on points back to our current working directory unless we say otherwise.

## Moving Around with CD / CHDIR

`cd` and `chdir` do more than report where we are. Hand either of them a path and they'll move us to it. That path can be relative to where we're standing right now, or it can be absolute, starting all the way back at the root of the drive.

If you've spent time in Linux, this is the same relative-versus-absolute idea you already know. If it's new, here's the short version.

First we need the root directory. The simplest way to think about root is the topmost directory, the one that holds everything else. On Windows that's `C:\`.

> [!NOTE]
> **Why C:?** `C:\` has been the root on Windows machines going back to the MS-DOS and Windows 3.0 days. Back then `A:\` and `B:\` were reserved for floppy drives, so `C:\` became the first internal hard drive, and the convention stuck.

### Absolute Path

An absolute path spells out the whole route from the root to where we want to be.

```powershell
C:\Users\student\Desktop> cd C:\Windows\System32

C:\Windows\System32>
```

We started in `C:\Users\student\Desktop`, handed `cd` a path that begins at the root `C:\` and walks all the way down to `\System32`. That path traces the full structure from root to destination, so we'd call `C:\Windows\System32` the absolute path.

### Relative Path

A relative path starts from wherever we already are instead of from the root.

```powershell
C:\Users\student> cd .\Pictures

C:\Users\student\Pictures>
```

Here the `.` stands for our current directory, and `\Pictures` is the folder sitting inside it. Since the whole thing is described in relation to where we're standing, that's a relative path.

A couple of shorthand symbols make relative paths fast to work with.

| Symbol | Meaning |
|---|---|
| `.` | The current directory |
| `..` | The parent directory, one level up |

Once we're comfortable with `..`, we can climb several levels at once. Say we're sitting in `C:\Users\student\Pictures` and we want to get back to the root in a single move.

```powershell
C:\Users\student\Pictures> cd ..\..\..\

C:\>
```

Each `..` walks us up one level, so three of them takes us from `\Pictures` back to `C:\` in one stroke. This is worth practicing now while the structure is still fresh, because moving up and down the tree quickly is something we'll lean on constantly.

## Exploring the File System

Now that we can move, we should branch out and actually look around. Getting a real feel for the layout of a system, and the files living inside it, gives us a solid advantage in understanding whatever we're working on. The catch is that bouncing in and out of directories and running `dir` in every subfolder gets tedious fast. The `tree` command saves us that trouble by printing a path and all of its subdirectories in one shot.

```powershell
C:\Users\student> tree

Folder PATH listing
Volume serial number is XXXX-XXXX
C:.
├───3D Objects
├───Contacts
├───Desktop
├───Documents
├───Downloads
├───Favorites
│   └───Links
├───Music
├───Pictures
│   ├───Camera Roll
│   └───Saved Pictures
└───Videos
    └───Captures
```

By default `tree` shows us directories only. If we want the files listed alongside the folders, we add the `/F` switch.

```powershell
C:\Users\student> tree /F

Folder PATH listing
Volume serial number is XXXX-XXXX
C:.
├───Desktop
│       project-plan.txt
│       budget.xlsx
│
├───Documents
├───Downloads
└───Pictures
    ├───Camera Roll
    └───Saved Pictures
```

From an attacker's perspective this is a quick way to surface interesting files, things like configuration files, project folders, or in the worst case a file sitting there with credentials in it. The thing to keep in mind is that `tree /F` lists every file and folder under the path we give it, so on a real system it can throw a wall of output at us. In a later file we'll cover cleaner ways to handle that output and shape it into something more usable. For now, it's enough to know that once we've seen what we came for, we can cut the command off with `Ctrl + C`.

## Interesting Directories

Before we wrap up, let's talk through a few directories that tend to come up on a Windows host. The table below lists common spots an attacker might abuse to drop files, run reconnaissance, or map out the attack surface. Knowing where these are helps us on the offensive side, and it's just as useful as defenders, since these are the same places we'd want to watch.

| Variable | Location | Why it's interesting |
|---|---|---|
| `%SYSTEMROOT%\Temp` | `C:\Windows\Temp` | Global temp directory. Every user, regardless of privilege, has read, write, and execute here, which makes it handy for dropping files as a low-privilege account. |
| `%TEMP%` | `C:\Users\<user>\AppData\Local\Temp` | A user's personal temp directory, owned by that account. Useful once an attacker controls a local or domain-joined user. |
| `%PUBLIC%` | `C:\Users\Public` | Open to any interactive logon with full read, write, and execute. A quieter alternative to the global Temp directory since it's less likely to be watched. |
| `%ProgramFiles%` | `C:\Program Files` | Holds the 64-bit applications installed on the system. Good for seeing what's running on a target. |
| `%ProgramFiles(x86)%` | `C:\Program Files (x86)` | Holds the 32-bit applications. Same value for sizing up what's installed. |

That list is nowhere near complete, but these are the ones that come up often enough to commit to memory. From here we've got the skills to move around a Windows file system with confidence and keep track of where we are in relation to everything else. In the next file we'll shift toward pulling system information so we can understand the environment we've landed in.

## Quick Reference

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
| Stop runaway output | `Ctrl + C` |

---

*Study notes for personal and classroom use. Techniques described here are for authorized systems only.*
