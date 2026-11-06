# Gathering System Information with cmd.exe

> Windows command-line notes. Part of an ongoing command-line reference series.

Now that we can navigate a Windows host from the Command Prompt and work with its files, let's move on to a skill that both system administrators and penetration testers lean on constantly, gathering system information. This is also called host enumeration, and it's the step where we get to know the environment we've landed in.

Enumeration can feel daunting at first, but it pays off on both sides of the work. On the red team, scanning a host and its surroundings tells us which services and machines might be worth a closer look. On the blue team, the same information helps us diagnose issues, harden hosts and services, and keep an eye on the integrity of the network. Whichever side we're sitting on, this file aims to answer three things. What information can we pull from a host? Why does thorough enumeration matter? How do we gather it from the Command Prompt, and what methodology keeps us organized?

## What Can We Gather?

Once we've got a shell on a system, knowing where to even start can be the hard part. Poking around with no plan tends to burn hours and turn up very little. The goal of host enumeration is to build an overall picture of the target, its environment, and how it talks to other systems on the network.

It helps to group the information into a few categories, so we always know what we're hunting for.

| Category | What it covers |
|---|---|
| General system information | The overall picture of the host, such as the hostname, OS name and version, build and configuration, and installed patches and hotfixes. |
| Networking information | How the host connects, including its IP addresses, network interfaces, reachable subnets, DNS servers, known hosts, and shared network resources. |
| Domain information | Active Directory details for the domain the host is joined to. |
| User information | Local users and groups, plus what those accounts can reach, like environment variables, running tasks, scheduled tasks, and known services. |

That's not an exhaustive list of everything on a system, but it's enough to build a solid methodology. To keep ourselves on target, we can keep asking a few guiding questions as we go.

- What system information can we pull from this host?
- What other systems is this host talking to over the network?
- Which user accounts can we reach, and what's accessible from them?

Think of these as a way to build situational awareness and a repeatable process, so we have a clear sense of what we're looking for and what to filter out during a real engagement.

## Why It Matters

The point of enumeration is to give us a starting point and a guide for how to approach the system. Picture an assumed-breach engagement where we're handed initial access through what looks like an unprivileged account, and the task is to get the lay of the land and see whether we can escalate beyond that account. To do that well, we need a thorough understanding of the environment, such as which account we're on, what groups it belongs to, what privileges it holds, what it can reach over the network, and what tasks and services run under it.

Here's the part that's easy to miss. It's tempting to glance at a system, decide it's fully patched with no obvious CVEs, and move on. If we only chase missing patches, though, we skip right past the human configuration errors that show up constantly in real environments. That's why taking our time and gathering everything we can should rank higher than rushing to exploit something. As defenders, the flip side is just as true, since the same thoroughness is what helps us spot the weak configuration before someone else does.

## Casting a Wide Net with systeminfo

The Command Prompt gives us a one-stop shop for host information in the `systeminfo` command. It surfaces the hostname, IP addresses, domain membership, installed hotfixes, and a good deal more, which makes it a go-to for a sysadmin diagnosing a problem.

On the offensive side, it's a fast way to get oriented the moment we land on a host, and it keeps our footprint small. One command is quieter than three, so we're less likely to draw attention. The OS version, build number, and installed hotfixes it reports are exactly what we'd feed into a quick search to check whether a known exploit lines up with this host.

```powershell
C:\Users\student> systeminfo

Host Name:                 WKSTN-07
OS Name:                   Microsoft Windows 10 Pro
OS Version:                10.0.19045 N/A Build 19045
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Member Workstation
OS Build Type:             Multiprocessor Free
...
```

Knowing only one way to gather information is limiting, though, especially when certain commands get watched more closely than others. That's why it's worth having more than one route to the same facts.

## Examining the System

`systeminfo` dumps a lot to sift through. When we only need something basic like the hostname or OS version, the `hostname` and `ver` utilities are quicker.

`hostname` does exactly what its name says and prints the machine's hostname. `ver` prints the current OS version number. Used together, they give us a lighter-weight alternative for pulling basic system facts while we enumerate.

```powershell
C:\Users\student> hostname

WKSTN-07
```

```powershell
C:\Users\student> ver

Microsoft Windows [Version 10.0.19045.xxxx]
```

## Scoping the Network

Host facts are a start. Now let's look at how the target connects and what it can reach, which is invaluable whichever side we're on.

`ipconfig` displays the current TCP/IP configuration for the machine. Run with no parameters, it still gives us the essentials.

```powershell
C:\Users\student> ipconfig

Windows IP Configuration

Ethernet adapter Ethernet:

   Connection-specific DNS Suffix  . : corp.local
   IPv4 Address. . . . . . . . . . . : 10.10.20.14
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 10.10.20.1
```

Even without parameters we get the domain suffix, IPv4 address, subnet mask, and default gateway, which already tells us something about the networks this host belongs to. For the full picture, `ipconfig /all` lists every adapter along with the physical (MAC) address, DHCP settings, and DNS servers.

`ipconfig` is great for the host's own connectivity. When we want to see which other hosts this machine has talked to recently, the `arp` command is the one to reach for.

`arp` displays the entries in the Address Resolution Protocol cache. It can modify entries too, though that's beyond what we need here. The cache shows us hosts that have communicated with our target, which we can use to start mapping the network along each interface.

```powershell
C:\Users\student> arp /a

Interface: 10.10.20.14 --- 0x12
  Internet Address      Physical Address      Type
  10.10.20.1            00-e0-67-15-cf-43     dynamic
  10.10.20.25           54-9f-35-1c-3a-e2     dynamic
  10.10.20.40           00-0c-29-62-09-81     dynamic
  10.10.20.255          ff-ff-ff-ff-ff-ff     static
  224.0.0.22            01-00-5e-00-00-16     static
  239.255.255.250       01-00-5e-7f-ff-fa     static
```

## Understanding Our Current User

With some host and network facts in hand, we should get to know the account we're actually running as. `whoami` is one of the best tools for that. On its own it reports the domain and username of the logged-in account.

```powershell
C:\Users\student> whoami

corp\student
```

If the account isn't domain-joined, we'll see the NetBIOS name instead, which in most cases is just the hostname.

### Checking Our Privileges

`whoami` does more than name the account. The `/priv` switch lists the security privileges held by the current user, which tells us a lot about what we can actually do on the host.

```powershell
C:\Users\student> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                          State
============================= ==================================== ========
SeShutdownPrivilege           Shut down the system                 Disabled
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled
SeUndockPrivilege             Remove computer from docking station Disabled
SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled
SeTimeZonePrivilege           Change the time zone                 Disabled
```

Here we've mostly got a basic set with most privileges disabled, which is normal for a standard user. If any of these were misconfigured, or the account had been handed extra privileges, that could become a path toward escalation. As defenders, this is exactly the kind of thing we'd audit.

### Investigating Groups

Beyond privileges, it's worth checking which groups our account belongs to. That can reveal default built-in groups and, more interestingly, any custom groups our user was explicitly added to. The `/groups` switch handles it.

```powershell
C:\Users\student> whoami /groups
```

If the account sits only in the built-in groups it was created with, there's not much to leverage there. In plenty of environments, though, users pick up extra access through the groups they're placed in, so it's always worth a look. When we want everything at once, `whoami /all` rolls the user, privileges, and groups into a single output.

## Investigating Other Users and Groups

After we understand our own account, it's natural to branch out and see what other accounts exist. In most environments machines are domain-joined, which means any domain user can potentially log in to any host without a local account on it. The `net` command gives us a window into this.

`net user` lists the accounts on a host, shows details for a specific user, and can create or delete users.

```powershell
C:\Users\student> net user

User accounts for \\WKSTN-07

-------------------------------------------------------------------------------
Administrator            DefaultAccount           Guest
student                  WDAGUtilityAccount
The command completed successfully.
```

On a busier network we'd expect to see more accounts here. For groups, `net group` and `net localgroup` do the work. `net group` shows domain groups and has to be run against a domain controller, while `net localgroup` runs against any host and lists the groups it holds.

```powershell
C:\Users\student> net group
This command can be used only on a Windows Domain Controller.

C:\Users\student> net localgroup

Aliases for \\WKSTN-07

-------------------------------------------------------------------------------
*Administrators
*Backup Operators
*Guests
*Network Configuration Operators
*Performance Log Users
*Power Users
*Remote Desktop Users
*Remote Management Users
*Users
The command completed successfully.
```

## Exploring Network Resources

In a domain environment, users are often expected to keep their work on a network share rather than locally, and those shares usually live on a server. Standard users typically have the permissions to read, write, and execute on a share once they're authenticated. The question is how we find those shares in the first place.

`net share` displays the shared resources on the host and can create new ones.

```powershell
C:\Users\student> net share

Share name   Resource                        Remark

-------------------------------------------------------------------------------
C$           C:\                             Default share
IPC$                                         Remote IPC
ADMIN$       C:\Windows                      Remote Admin
Dept-Files   D:\Department                   Department file share
The command completed successfully.
```

From the remarks we can make an educated guess that `Dept-Files` is a manually mounted share that might hold something worth a look. If we found an open share like this on an engagement, we'd want to track whether we have permission to reach it, whether we can read, write, and execute there, and whether anything on it is actually valuable.

When we aren't hunting for a specific share and just want a broad look at the environment, `net view` lists the shared resources a host knows about, including domain resources, shares, and printers.

```powershell
C:\Users\student> net view
```

## Piecing It Together

Put all of this together and we've pulled a real amount of information about a host and its surroundings. From here, depending on our access, we can work toward escalating or keep moving toward the goal. System-level access on every host usually isn't the point for a pentester, so it's not worth getting stuck chasing it everywhere.

This is a quick look at how far CMD alone can carry an assessment with limited tooling. The honest caveat is that this route is loud. We're writing plenty of logs, leaving traces across hosts, and a competent blue team will notice. That matters for us as defenders too, and it's worth saying plainly. > [!NOTE]
> In a normal environment, a regular user running `cmd.exe` is already unusual, and a string of `net` commands is even more so. With solid monitoring and logging in place, these are the kinds of actions we should be able to catch early and use to triage an incident before it spreads.

A good methodology is the real takeaway here. Knowing the scope of what lives on a system, why we want it, and how to pull it quickly is a skill that carries into every assessment and every investigation. In the next file we'll build on this foundation and look at finding specific files and directories on a host.

## Quick Reference

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

*Study notes for personal and classroom use. Techniques described here are for authorized systems only.*
