# Working with Files and Directories in cmd.exe

> Windows command-line notes. Part of an ongoing command-line reference series.

Now that we can move around the command line safely, it's time to actually work with files and directories. This is a big topic, and Windows almost always gives us more than one way to get the same job done. We'll cover a solid core set here, but keep in mind there are other routes to most of these tasks. Let's dig in.

## Directories

So what's a directory? In plain terms it's the folder structure inside the Windows file system. Our files live nested inside those folders, and we move between them with the commands we practiced earlier, mostly `cd` and `dir`.

Going back to the hallway idea from the last file for a second, here's one way to picture it.

- The drive itself is the disk, and it's also the root directory. Think of the `C:` drive as a hotel.
- That hotel has floors full of hallways. This level holds directories like `Windows`, `Users`, and `Program Files`, along with anything the OS or a user created.
- Each floor has multiple halls. Picture every hall as a folder nested inside those bigger directories. Under `Users`, for instance, we'd find a folder for each account on the host. By now we're a few levels deep, something like `C:\Users\student`.
- This keeps branching as the host gets used and more software goes on.
- Eventually we reach the room we were after and look inside. The door is a file sitting within the directory.

### Viewing and Listing Directories

We already have a head start here. `cd` tells us which directory we're in, `dir` lists the files inside it, and `tree` gives us a full picture of every folder and file under a path. `chdir` is just another name for `cd` and changes our working directory the same way.

### Create a New Directory

Making a directory is about as simple as it gets. We've got two commands for it, `md` and `mkdir`, and they do the exact same thing, so use whichever you like.

```powershell
C:\Users\student\Desktop> md new-directory

C:\Users\student\Desktop> mkdir another-directory
```

A quick `dir` afterward confirms both folders now exist on the Desktop.

### Delete a Directory

To remove a directory we use `rd` or `rmdir`. These two are built specifically for removing directory trees, so they don't deal with individual files or attributes.

If the directory still has anything in it, a plain `rd` will stop and tell us so.

```powershell
C:\Users\student\Desktop> rd project-files
The directory is not empty.
```

That's where the `/S` switch comes in. It clears the directory along with everything inside it, after a confirmation prompt.

```powershell
C:\Users\student\Desktop> rd /S project-files
project-files, Are you sure (Y/N)? Y
```

So if you ever get stuck on a "directory is not empty" warning, remember the `/S` switch. `rmdir` behaves identically to `rd` in every one of these cases.

### Move and Copy Directories

Changing a directory is a bit more involved than changing a file, since the directory carries other files and folders inside it. We've got a few options here, `move`, `xcopy`, and `robocopy`.

`move` takes a directory, along with whatever files live in it, and relocates the whole thing from the source to the destination we name.

```powershell
C:\Users\student\Desktop> move example C:\Users\student\Documents\example

        1 dir(s) moved.
```

A `dir` on the Documents folder afterward confirms the `example` directory landed there.

`xcopy` still ships with current Windows, though it's worth knowing it has been deprecated in favor of `robocopy`. Where `xcopy` earns its spot is its handling of attributes. The syntax follows the pattern `xcopy source destination options`, and like `move`, we can use wildcards on the source but not the destination.

```powershell
C:\Users\student\Desktop> xcopy C:\Users\student\Documents\example C:\Users\student\Desktop\ /E
```

The `/E` switch tells `xcopy` to copy all files and subdirectories, including empty ones. This doesn't delete the original copy. By default `xcopy` resets the file's attributes during the copy, so if we want to keep things like read-only or hidden, we add the `/K` switch.

From an offensive point of view, `xcopy` can pull a copy of a file that's locked or otherwise stubborn without dragging extra tools onto the host. As defenders, that same behavior is handy for grabbing a file in its current state for analysis, say a read-only file that came in from a flash drive and is now acting suspicious.

`robocopy` is the successor to `xcopy` and it's built with a lot more capability. A good way to think about it is the best parts of `copy`, `xcopy`, and `move` rolled together with extras on top. It can copy and move files locally, to other drives, and even across a network, all while preserving file data and attributes like timestamps, ownership, ACLs, and flags such as hidden or read-only. One thing to know is that `robocopy` was designed for large directories and drive syncing, so by default it isn't eager to handle single files. That doesn't mean it can't.

```powershell
C:\Users\student\Desktop> robocopy C:\Users\student\Desktop C:\Users\student\Documents\
```

That command mirrors the contents of the Desktop into Documents. It runs cleanly here because we have permission over the folder we're copying.

A couple of `robocopy` switches are worth knowing up front.

| Switch | What it does |
|---|---|
| `/E` | Copies all subdirectories, including empty ones. |
| `/MIR` | Mirrors the full directory tree. It can delete files in the destination, so handle it carefully. |
| `/B` | Backup mode, which needs the Backup and Restore user right. |
| `/L` | A what-if run. It shows the result without actually doing anything. |
| `/A-:SH` | Clears the system and hidden attributes it would otherwise set. |

> [!WARNING]
> The `/MIR` switch mirrors the destination to the source, which means any file already sitting in the destination gets removed. Point it at a clean, empty folder.

The `/L` switch pairs nicely with it, since it lets us preview exactly what a command would do before we commit to it. `robocopy` has enough depth that it really deserves its own write-up, so experiment with it and build out your own patterns.

## Files

A lot of the same commands we used on directories work on files too, and Windows hands us plenty of built-in tools for file work. We'll cover a useful handful. First up, viewing files and their contents.

### List Files and View Their Contents

We already know `dir` lists the files in a directory, and `tree /F` shows every directory and file under a path. What we haven't covered is reading what's actually inside a file. For that we've got `more`, `type`, and `openfiles`.

`more` lets us view a file, or the output of another command, one screen at a time. Think of it as a buffer for text that would otherwise scroll right off the top of the terminal.

```powershell
C:\Users\student\Documents> more report.txt

-- More (6%) --
```

That percentage at the bottom tells us how far into the file we are. Pressing Enter or the space bar scrolls us further through it. If a file has a lot of blank lines or big gaps, the `/S` switch crunches that empty space down to a single line at each spot, which makes it easier to read. It doesn't change the file, it just changes how `more` displays it.

`more` really shines when we pipe a noisy command into it to slow the output down.

```powershell
C:\Users\student> ipconfig /all | more
```

That takes the wall of text from `ipconfig /all` and feeds it through `more` so we can read it one screen at a time. It's a lifesaver with commands that dump a ton of output, like `systeminfo`.

`openfiles` shows which files are open on the local machine or a remote host, and which user has them. It needs administrator privileges, and it isn't enabled by default on Windows. With it we can view open files, disconnect them, and even kick a user off a file they're holding.

`type` displays the contents of one or more text files at once, and it supports redirection too. It's a simple tool but a genuinely handy one. A nice property of `type` is that it doesn't lock files, so there's no risk of stepping on something while we read it.

```powershell
C:\Users\student\Desktop> type lab-notes.txt
```

We can also point `type` at redirection to write or append data, which we'll get to in the I/O section.

Poking around a host with built-in tools like `type` and `more` is a quick and fairly quiet way to hunt for passwords, rosters, or other sensitive information, which is exactly why it matters to us on both sides of the work.

### Create and Modify a File

Creating and changing a file from the command line is straightforward. We've got several options, including `echo`, `fsutil`, and `ren`.

`echo` paired with output redirection either creates a new file or modifies one that already exists.

```powershell
C:\Users\student\Desktop> echo Check out this text > demo.txt

C:\Users\student\Desktop> echo More text for our demo file >> demo.txt

C:\Users\student\Desktop> type demo.txt
Check out this text
More text for our demo file
```

A single `>` creates the file or overwrites it, while `>>` appends to what's already there. More on those operators in a moment.

`fsutil` can do a lot of things, and one of them is creating a file of a set size.

```powershell
C:\Users\student\Desktop> fsutil file createNew placeholder.txt 222
File C:\Users\student\Desktop\placeholder.txt is created
```

`ren` renames a file, and `rename` is just another name for the same command.

```powershell
C:\Users\student\Desktop> ren demo.txt report.txt
```

That renames `demo.txt` to `report.txt`. Simple enough.

## Input and Output

We've bumped into these a few times already, so let's slow down and talk about I/O directly. The operators `<`, `>`, `|`, and `&` let us route input and output between the console, files, and commands however we need.

### Send Output to a File

`>` pushes a command's output into a file. It creates the file if it doesn't exist, or overwrites the file's contents if it does.

```powershell
C:\Users\student\Documents> ipconfig /all > details.txt
```

That captures the full `ipconfig /all` output into `details.txt`. To add to a file instead of overwriting it, we use `>>`.

```powershell
C:\Users\student\Documents> echo a b c d e > test.txt

C:\Users\student\Documents> echo f g h i j k and more text >> test.txt

C:\Users\student\Documents> type test.txt
a b c d e
f g h i j k and more text
```

The first line creates `test.txt`, and the second appends to it with `>>`.

### Feed a File into a Command

We were sending output out of a command. Now let's feed input into one with `<`.

```powershell
C:\Users\student\Documents> find /i "and" < test.txt

f g h i j k and more text
```

Here we took the contents of `test.txt` and fed it into `find`, searching for the string `and`. It kicked back the line where the match lived. These are simple examples, but the same idea scales up to searching big text files for keywords, sorting for unique items, and more, which is a real help when we're hunting for key information.

### Pipe Output Between Commands

The pipe operator `|` feeds the output of one command straight into another.

```powershell
C:\Users\student\Documents> ipconfig /all | find /i "IPv4"

   IPv4 Address. . . . . . . . . . . : 172.16.146.5(Preferred)
```

We ran `ipconfig /all` and handed its output to `find` to pull out the line with `IPv4`. Once this concept clicks, the number of things we can chain together is pretty much endless.

### Chain Commands Together

Sometimes we want to run two commands back to back. Here's how the operators differ.

| Operator | Meaning |
|---|---|
| `&` | Run command A, then run command B no matter what. |
| `&&` | Run command A, and only run command B if A succeeded. |
| `\|\|` | Run command A, and only run command B if A failed. |

A plain `&` just fires both in order and doesn't care whether the first one worked.

```powershell
C:\Users\student\Documents> ping 8.8.8.8 & type test.txt
```

`&&` is the one to reach for when the second step depends on the first succeeding.

```powershell
C:\Users\student\Documents> cd C:\Users\student\Documents\Backup && echo backup complete > status.txt
```

We asked to change directory, and only on success did it write the file. The path changing and the file holding our text both tell us it worked. `||` flips that logic, running the second command only when the first one fails.

## Deleting Files

To remove files we use `del` or `erase`. Like a few other pairs we've seen, these two are the same command under different names, so treat them as interchangeable.

```powershell
C:\Users\student\Desktop\example> del file-1

C:\Users\student\Desktop\example> erase file-3 file-5
```

We can hand `del` or `erase` a single file, a list of files, a directory, or even a specific attribute to target. Above, `del` removed one file and `erase` removed a list of two.

### Deleting by Attribute

Say we want to clear a read-only or hidden file. The `/A:` switch selects files to delete based on an attribute. A quick look at `help del` lays out the attribute letters.

| Attribute | Meaning |
|---|---|
| `R` | Read-only |
| `H` | Hidden |
| `S` | System |
| `A` | Ready for archiving |
| `-` | Prefix meaning "not" |

So `/A:R` targets read-only files. The question is how we spot which files carry which attribute in the first place. `dir` comes to the rescue again, since `dir /A:R` lists everything with the read-only attribute.

```powershell
C:\Users\student\Desktop\example> dir /A:R
```

Once we know what's read-only, we can clear it. The `*` wildcard says "any file."

```powershell
C:\Users\student\Desktop\example> del /A:R *
```

Hidden files work the same way. `dir /A:H` reveals them, and here's the thing to remember. Much like Linux, a hidden file still exists and can still be called by name, it just stays out of a normal directory listing and the GUI unless we go looking for it. Swapping the attribute from `R` to `H` deletes it.

```powershell
C:\Users\student\Desktop\example> dir /A:H

C:\Users\student\Desktop\example> del /A:H *
```

To wipe a directory and everything in it, we can feed `del` the directory name to clear the files, then follow up with `rd` to remove the structure itself. If a file in there is read-only or carries another attribute, the `/F` switch force-deletes it.

## Copying and Moving Files

Just like with directories, we've got a few ways to copy or move files, and `copy` and `move` are the easiest. We can duplicate a file in place or send it somewhere else.

`copy` makes a copy at the destination, and it can rename the file along the way.

```powershell
C:\Users\student\Documents\Backup> copy lab-notes.txt C:\Users\student\Downloads\lab-notes-copy.txt

        1 file(s) copied.
```

By default `copy` finishes and closes. If we want to confirm the copy came through correctly, the `/V` switch turns on file validation.

```powershell
C:\Windows\System32> copy calc.exe C:\Users\student\Downloads\copied-calc.exe /V
```

`move` relocates files and directories, and it can rename them too. The difference from `copy` is that `move` doesn't leave the original behind, and it can rename and move directories as well.

```powershell
C:\Users\student\Desktop> move C:\Users\student\Desktop\lab-notes.txt C:\Users\student\Downloads

        1 file(s) moved.
```

That took `lab-notes.txt` and moved it into Downloads. Handling files really is that approachable once the commands are familiar.

## Quick Reference

| Task | Command |
|---|---|
| Create a directory | `md` or `mkdir` |
| Delete an empty directory | `rd` or `rmdir` |
| Delete a directory and contents | `rd /S <dir>` |
| Move a directory | `move <src> <dest>` |
| Copy with attributes (legacy) | `xcopy <src> <dest> /E` |
| Robust copy / sync | `robocopy <src> <dest>` |
| Preview a robocopy run | `robocopy ... /L` |
| View a file one screen at a time | `more <file>` |
| Display file contents | `type <file>` |
| See open files (admin) | `openfiles` |
| Create or overwrite a file | `echo text > file` |
| Append to a file | `echo text >> file` |
| Create a sized file | `fsutil file createNew <name> <size>` |
| Rename a file | `ren` or `rename` |
| Feed a file into a command | `command < file` |
| Pipe output between commands | `cmd1 \| cmd2` |
| Run B after A always | `A & B` |
| Run B only if A succeeds | `A && B` |
| Run B only if A fails | `A \|\| B` |
| Delete a file | `del` or `erase` |
| List files by attribute | `dir /A:R` or `dir /A:H` |
| Delete files by attribute | `del /A:R *` |
| Force delete | `del /F <file>` |
| Copy a file (with validation) | `copy <src> <dest> /V` |
| Move a file | `move <src> <dest>` |

---

*Study notes for personal and classroom use. Techniques described here are for authorized systems only.*
