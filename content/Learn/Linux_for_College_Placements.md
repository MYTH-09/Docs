# Linux for College Placements — Beginner to Interview Ready

This guide assumes you are starting from **zero Linux knowledge**.

The goal is not just to memorize commands. You should understand:

- what Linux is
- how the Linux filesystem works
- how to move around the terminal
- how to create, copy, move, delete, and inspect files
- how permissions work
- how to search and process text
- how to inspect and control processes
- how basic networking commands work
- how Linux services and packages are managed
- what interviewers commonly ask about these topics

Examples use **Ubuntu/Debian-style Linux**, because commands such as `apt` and `systemctl` are common there.

---

# 1. First: What Is Linux?

## 1.1 Operating system

An operating system (OS) is the software that manages:

- CPU
- RAM
- disks
- files
- network
- devices
- applications

Examples of operating systems:

- Windows
- macOS
- Linux
- Android

## 1.2 What is Linux?

Strictly speaking, **Linux is the kernel**.

The kernel is the core part of an operating system. It communicates with hardware and provides services to programs.

A complete Linux-based operating system usually contains:

- Linux kernel
- system utilities
- package manager
- libraries
- shell
- applications

Examples of Linux distributions:

- Ubuntu
- Debian
- Fedora
- Arch Linux
- Red Hat Enterprise Linux (RHEL)
- Linux Mint

A distribution ("distro") packages the Linux kernel together with other software to create a usable operating system.

---

# 2. Why Linux Matters for Placements

Linux is extremely common in:

- backend development
- cloud computing
- DevOps
- servers
- containers
- cybersecurity
- databases
- CI/CD
- production systems

Even if you apply for a normal software-development role, basic Linux skills are valuable.

Interviewers may ask things such as:

- What is a process?
- What is the difference between a process and a thread?
- What does `chmod 755` mean?
- How do you find a file?
- How do you search text inside files?
- How do you check whether a service is running?
- How do you kill a process?
- What is SSH?
- What is the difference between `>` and `>>`?
- What does `sudo` do?
- What is the root directory?
- What is the difference between absolute and relative paths?

---

# 3. The Terminal and Shell

When you open a terminal, you interact with a **shell**.

A shell is a program that accepts commands and executes them.

Common shells include:

- Bash
- Zsh
- Fish

Bash is one of the most common shells on Linux.

A command usually looks like:

```bash
command [options] [arguments]
```

Example:

```bash
ls -l /home
```

Here:

- `ls` = command
- `-l` = option
- `/home` = argument

You may see a prompt such as:

```text
user@computer:~$
```

Meaning:

- `user` = current user
- `computer` = hostname
- `~` = current user's home directory
- `$` = normal user shell

A root shell commonly uses:

```text
#
```

instead of:

```text
$
```

---

# 4. Important Linux Concepts Before Commands

## 4.1 Current working directory

At any moment, your terminal is "inside" one directory.

That is your **current working directory**.

Example:

```text
/home/user/projects
```

If you run:

```bash
ls
```

Linux normally lists the contents of the current directory.

---

## 4.2 Absolute path

An absolute path starts from `/`, the root directory.

Example:

```text
/home/user/projects/app/main.py
```

This path identifies a location independently of your current directory.

---

## 4.3 Relative path

A relative path is interpreted from your current directory.

Suppose you are here:

```text
/home/user/projects
```

Then:

```text
app/main.py
```

is a relative path.

---

## 4.4 Special path symbols

### `.`

Means:

```text
current directory
```

Example:

```bash
./script.sh
```

### `..`

Means:

```text
parent directory
```

Example:

```bash
cd ..
```

### `~`

Means:

```text
current user's home directory
```

Example:

```bash
cd ~
```

### `/`

Means:

```text
root directory
```

Example:

```bash
cd /
```

---

# 5. Linux Filesystem Basics

Linux has one directory tree.

At the top is:

```text
/
```

This is called the **root directory**.

A simplified filesystem looks like:

```text
/
├── bin
├── boot
├── dev
├── etc
├── home
│   └── user
├── lib
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin
├── tmp
├── usr
└── var
```

Important directories:

### `/home`

Normal users' home directories.

Example:

```text
/home/user
```

### `/root`

Home directory of the root user.

This is different from `/`.

- `/` = root of the filesystem
- `/root` = root user's home directory

### `/etc`

System and application configuration files.

### `/var`

Frequently changing data such as logs, caches, and application data.

### `/tmp`

Temporary files.

### `/usr`

Many user-space programs, libraries, and shared resources.

### `/bin`

Essential command binaries on many systems.

On modern Ubuntu systems, `/bin` may be linked into `/usr/bin` as part of the merged-/usr layout.

### `/dev`

Device files.

### `/proc`

A virtual filesystem containing information about processes and the kernel.

---

# 6. Navigation

Commands in this section:

- `pwd`
- `ls`
- `cd`
- `tree`
- `find`

---

# 7. `pwd` — Print Working Directory

## What it does

Shows your current directory.

## Syntax

```bash
pwd
```

## Example

```bash
pwd
```

Output:

```text
/home/user/projects
```

This tells you exactly where you are.

## Why it matters

When you are confused about your location, run:

```bash
pwd
```

---

# 8. `ls` — List Directory Contents

## Basic usage

```bash
ls
```

Shows files and directories.

Example:

```bash
ls
```

Output:

```text
app  notes.txt  script.sh
```

---

## Long listing

```bash
ls -l
```

Example:

```text
-rw-r--r-- 1 user user 1200 Sep 8 10:30 notes.txt
```

You will learn permissions later.

---

## Show hidden files

```bash
ls -a
```

Linux hidden files normally begin with `.`.

Example:

```text
.
..
.bashrc
.config
notes.txt
```

---

## Long + hidden

```bash
ls -la
```

---

## Human-readable file sizes

```bash
ls -lh
```

Example:

```text
-rw-r--r-- 1 user user 1.2K Sep 8 10:30 notes.txt
```

---

## Useful combinations

```bash
ls -lah
```

Meaning:

- `-l` = long listing
- `-a` = include hidden files
- `-h` = human-readable sizes

---

## List a specific directory

```bash
ls /home
```

or:

```bash
ls ~/Downloads
```

---

# 9. `cd` — Change Directory

## Basic usage

```bash
cd directory
```

Example:

```bash
cd projects
```

---

## Move to parent

```bash
cd ..
```

If you are in:

```text
/home/user/projects
```

then:

```bash
cd ..
```

moves you to:

```text
/home/user
```

---

## Go home

```bash
cd ~
```

You can also usually just run:

```bash
cd
```

---

## Go to root

```bash
cd /
```

---

## Absolute path

```bash
cd /home/user/projects
```

---

## Go back to previous directory

```bash
cd -
```

This is extremely useful.

Example:

```bash
cd /tmp
cd /home/user
cd -
```

The last command returns to `/tmp`.

---

# 10. `tree` — Display Directory Structure

`tree` shows directories and files in a tree-like structure.

Example:

```bash
tree
```

Output:

```text
.
├── app
│   ├── main.py
│   └── config.py
├── README.md
└── requirements.txt
```

## Limit depth

```bash
tree -L 2
```

Shows only two directory levels.

## If `tree` is not installed

On Ubuntu/Debian:

```bash
sudo apt install tree
```

---

# 11. `find` — Search for Files and Directories

`find` is one of the most important Linux commands.

## Basic syntax

```bash
find [starting-path] [conditions]
```

---

## Find by name

```bash
find . -name "notes.txt"
```

Searches from the current directory.

---

## Find directories

```bash
find . -type d -name "project"
```

`-type d` means directory.

---

## Find files

```bash
find . -type f -name "*.txt"
```

Meaning:

- `.` = search here
- `-type f` = files only
- `-name "*.txt"` = names ending in `.txt`

---

## Case-insensitive name search

```bash
find . -iname "README.md"
```

---

## Find files by size

Example:

```bash
find . -type f -size +100M
```

Find files larger than 100 MB.

---

## Find recently modified files

Example:

```bash
find . -type f -mtime -1
```

Roughly means files modified within the last day.

---

## Interview point

`find` searches the filesystem dynamically.

Do not confuse it with `grep`.

- `find` → searches for files/directories
- `grep` → searches text/content

---

# 12. Files and Directories

Commands:

- `cp`
- `mv`
- `rm`
- `chmod`
- `chown`
- `ln`

---

# 13. `cp` — Copy

## Copy a file

```bash
cp source.txt destination.txt
```

Example:

```bash
cp notes.txt notes_backup.txt
```

---

## Copy into a directory

```bash
cp notes.txt backups/
```

---

## Copy a directory

Use recursive mode:

```bash
cp -r project project_backup
```

`-r` means recursive.

---

## Preserve attributes

```bash
cp -p file1 file2
```

Useful attributes such as timestamps and permissions are preserved where possible.

---

# 14. `mv` — Move or Rename

`mv` can do two major things:

1. move a file
2. rename a file

## Move

```bash
mv file.txt documents/
```

## Rename

```bash
mv old.txt new.txt
```

There is no separate `rename` requirement for basic usage because `mv` handles common renaming tasks.

---

# 15. `rm` — Remove

## Delete a file

```bash
rm file.txt
```

Be careful: deleting files from the terminal normally does not move them to a graphical recycle bin.

---

## Delete multiple files

```bash
rm file1.txt file2.txt
```

---

## Interactive deletion

```bash
rm -i file.txt
```

Asks before deleting.

---

## Delete a directory and its contents

```bash
rm -r directory
```

Recursive deletion.

---

## Force deletion

```bash
rm -f file.txt
```

Force removal where permissions allow it.

---

## Extremely dangerous command

```bash
rm -rf ...
```

`-r` = recursive

`-f` = force

Never casually run commands such as:

```bash
rm -rf /
```

or commands with an incorrectly constructed path.

The main lesson is:

> Always understand the target path before using recursive deletion.

---

# 16. `chmod` — Change File Permissions

Permissions are a very important Linux interview topic.

Every file typically has permissions for:

- owner
- group
- others

Three basic permission types:

- `r` = read
- `w` = write
- `x` = execute

Example:

```text
-rwxr-xr--
```

Breakdown:

```text
- rwx r-x r--
  --- --- ---
  user group others
```

Meaning:

- owner: `rwx`
- group: `r-x`
- others: `r--`

---

# 17. Permission Numbers

Linux commonly represents permissions numerically.

Values:

| Permission | Value |
|---|---:|
| `r` | 4 |
| `w` | 2 |
| `x` | 1 |

Add the values together.

## Examples

`r--`

```text
4
```

`rw-`

```text
4 + 2 = 6
```

`r-x`

```text
4 + 1 = 5
```

`rwx`

```text
4 + 2 + 1 = 7
```

Therefore:

```text
755
```

means:

```text
owner  = 7 = rwx
group  = 5 = r-x
others = 5 = r-x
```

---

# 18. Common `chmod` Commands

## Make a script executable

```bash
chmod +x script.sh
```

Then run:

```bash
./script.sh
```

---

## Numeric permissions

```bash
chmod 755 script.sh
```

---

## Private file

```bash
chmod 600 secret.txt
```

Meaning:

- owner: read + write
- group: no permissions
- others: no permissions

---

## Symbolic permissions

```bash
chmod u+x script.sh
```

`u` = user/owner.

```bash
chmod g+w file.txt
```

`g` = group.

```bash
chmod o-r file.txt
```

`o` = others.

---

# 19. Directory Permissions

Directory permissions have a special practical meaning.

For a directory:

- `r` = list directory entries
- `w` = create/delete/rename entries, subject to other rules
- `x` = access/traverse the directory

This is why directory permissions can initially feel different from file permissions.

---

# 20. `chown` — Change Ownership

`chown` means **change owner**.

## Syntax

```bash
chown owner file
```

Example:

```bash
sudo chown user notes.txt
```

---

## Change owner and group

```bash
sudo chown user:developers project
```

---

## Recursive ownership change

```bash
sudo chown -R user:developers project/
```

Be very careful with `-R`.

Changing ownership of the wrong system directory can break applications or the operating system.

---

# 21. Owner vs Group

Suppose:

```text
-rwxr-xr--
```

The first permission block belongs to:

```text
owner
```

The second belongs to:

```text
group
```

The third belongs to:

```text
others
```

Use `ls -l` to inspect ownership:

```bash
ls -l
```

Example:

```text
-rw-r--r-- 1 alice developers 1200 Sep 8 10:30 file.txt
```

Here:

- owner = `alice`
- group = `developers`

---

# 22. `ln` — Create Links

Linux supports links.

Two important types:

1. hard links
2. symbolic (soft) links

---

# 23. Symbolic Link

Create a symbolic link:

```bash
ln -s target link_name
```

Example:

```bash
ln -s /home/user/project/current config
```

`config` points to the target.

Think of it somewhat like a shortcut, although symbolic links are a filesystem feature with important differences from desktop shortcuts.

---

# 24. Hard Link

Create a hard link:

```bash
ln original.txt copy.txt
```

A hard link points to the same underlying inode/data as the original file.

Important practical differences:

### Symbolic link

- points to a pathname
- can usually cross filesystems
- can point to directories in normal usage
- can become dangling if the target path disappears

### Hard link

- references the same inode
- normally cannot cross filesystem boundaries
- normally cannot be created for directories by ordinary users
- remains usable if another filename pointing to the inode is removed

---

# 25. What Is an Inode?

An inode is a filesystem data structure containing metadata about a file.

Conceptually, it stores information such as:

- file type
- permissions
- owner
- group
- timestamps
- links count
- pointers/references to file data

The filename is associated with the inode through the directory structure.

This is important for understanding hard links.

---

# 26. Text Processing

Commands:

- `cat`
- `less`
- `grep`
- `sed`
- `awk`

These commands become especially powerful when combined with pipes.

---

# 27. `cat` — Display File Contents

## Basic usage

```bash
cat notes.txt
```

Displays the entire file.

---

## Combine files

```bash
cat part1.txt part2.txt
```

---

## Create a small file interactively

```bash
cat > notes.txt
```

Type content, then press:

```text
Ctrl+D
```

to finish input.

---

## Append using `>>`

```bash
cat >> notes.txt
```

This appends input instead of replacing the file.

---

# 28. Output Redirection

This is an important shell concept.

## `>`

Redirects output to a file, replacing existing contents.

```bash
echo "hello" > file.txt
```

## `>>`

Appends output.

```bash
echo "world" >> file.txt
```

---

# 29. `less` — Read Large Files

For large files, `less` is usually better than `cat`.

```bash
less application.log
```

Useful keys inside `less`:

- `Space` → next page
- `b` → previous page
- `/text` → search
- `n` → next search result
- `q` → quit

This is particularly useful for log files.

---

# 30. `grep` — Search Text

`grep` is one of the most important Linux commands.

## Basic syntax

```bash
grep [options] pattern file
```

Example:

```bash
grep "ERROR" application.log
```

Shows lines containing `ERROR`.

---

## Case-insensitive

```bash
grep -i "error" application.log
```

---

## Show line numbers

```bash
grep -n "ERROR" application.log
```

---

## Search recursively

```bash
grep -r "TODO" .
```

Searches files under the current directory.

A commonly useful variation is:

```bash
grep -R "TODO" .
```

---

## Invert match

```bash
grep -v "DEBUG" application.log
```

Shows lines that do not contain `DEBUG`.

---

## Count matches

```bash
grep -c "ERROR" application.log
```

---

## Show only matching filenames

```bash
grep -l "ERROR" *.log
```

---

# 31. `grep` with Pipes

A pipe is:

```text
|
```

It sends the output of one command into another command's input.

Example:

```bash
ps aux | grep python
```

Meaning:

1. `ps aux` produces process information
2. `grep python` filters that output

Pipes are fundamental to Linux command-line usage.

---

# 32. `sed` — Basic Text Transformation

`sed` is a stream editor.

A very common usage is replacement.

## Replace first occurrence on each line

```bash
sed 's/old/new/' file.txt
```

This normally prints the modified text to the terminal without changing the original file.

---

## Replace all occurrences on each line

```bash
sed 's/old/new/g' file.txt
```

`g` means global within each input line.

---

## Edit the file directly

On GNU/Linux:

```bash
sed -i 's/old/new/g' file.txt
```

Be careful with `-i` because it modifies the original file.

A safer habit during learning is to first run:

```bash
sed 's/old/new/g' file.txt
```

and inspect the output.

---

# 33. `sed` Delete Lines

Delete line 2:

```bash
sed '2d' file.txt
```

Delete lines 2 through 5:

```bash
sed '2,5d' file.txt
```

Again, without `-i`, this prints transformed output rather than modifying the original.

---

# 34. `awk` — Basic Text Processing

`awk` is especially useful for structured text.

It processes input line by line and splits records into fields.

Suppose:

```text
Alice 90
Bob 85
Charlie 92
```

Run:

```bash
awk '{print $1}' scores.txt
```

Output:

```text
Alice
Bob
Charlie
```

`$1` means first field.

---

## Print second field

```bash
awk '{print $2}' scores.txt
```

---

## Print multiple fields

```bash
awk '{print $1, $2}' scores.txt
```

---

## Field separator

Suppose a file uses commas:

```text
Alice,90
Bob,85
Charlie,92
```

Use:

```bash
awk -F',' '{print $1, $2}' scores.csv
```

`-F','` says comma is the field separator.

---

## Filter rows

```bash
awk '$2 > 90 {print $1}' scores.txt
```

This prints names whose score is greater than 90.

---

# 35. `sed` vs `awk` vs `grep`

A simple mental model:

### `grep`

> Find lines matching a pattern.

### `sed`

> Transform/edit text streams.

### `awk`

> Process structured text and fields.

They overlap, but this mental model is excellent for interviews and day-to-day use.

---

# 36. Processes

A **process** is a running instance of a program.

For example, when you run:

```bash
python app.py
```

Linux starts a process for that running program.

Each process has things such as:

- PID
- parent process
- memory
- open files
- environment
- scheduling state

---

# 37. PID

PID means:

> Process ID

Each running process has a process identifier.

Example:

```text
PID
1234
```

You use PIDs when inspecting or terminating processes.

---

# 38. `ps` — Process Snapshot

`ps` shows information about processes.

## Basic

```bash
ps
```

Usually shows processes associated with the current shell/session.

---

## More detailed common form

```bash
ps aux
```

This displays a broader list of running processes.

Typical columns include:

- USER
- PID
- %CPU
- %MEM
- STAT
- START
- TIME
- COMMAND

---

## Another common form

```bash
ps -ef
```

This uses a different option style and output format.

You may encounter both:

```bash
ps aux
```

and:

```bash
ps -ef
```

in interviews and production systems.

---

# 39. Find a Process with `ps` + `grep`

Example:

```bash
ps aux | grep nginx
```

This searches the process list for `nginx`.

One limitation is that `grep nginx` itself can sometimes appear in the output.

Later you may learn more specialized tools such as `pgrep`.

---

# 40. `top` — Live Process Monitor

Run:

```bash
top
```

It continuously updates process information.

It can show:

- CPU usage
- memory usage
- system load
- process list
- process states

Press:

```text
q
```

to quit.

---

# 41. `htop` — Friendlier Process Monitor

Run:

```bash
htop
```

It is an interactive process viewer with a more user-friendly interface.

It may not be installed by default.

Ubuntu/Debian:

```bash
sudo apt install htop
```

---

# 42. `top` vs `htop`

### `top`

- commonly available
- lightweight
- terminal-based
- standard on many systems

### `htop`

- more interactive
- easier to navigate
- visually clearer for many users

For placement interviews, know both names and the general purpose.

---

# 43. `kill` — Send a Signal to a Process

Despite its name, `kill` does not always mean "forcefully destroy".

It sends a **signal** to a process.

Example:

```bash
kill 1234
```

This normally sends `SIGTERM` (signal 15).

---

# 44. SIGTERM vs SIGKILL

## SIGTERM

```bash
kill 1234
```

Requests that the process terminate gracefully.

The application can handle the signal and clean up.

## SIGKILL

```bash
kill -9 1234
```

This sends `SIGKILL`.

A process cannot catch or ignore `SIGKILL`.

Use it when a process is not responding to normal termination.

A good progression is:

```bash
kill PID
```

and only then consider:

```bash
kill -9 PID
```

Do not make `kill -9` your default.

---

# 45. Other Useful Signals

List signals:

```bash
kill -l
```

Common signals:

- `SIGTERM` = 15
- `SIGKILL` = 9
- `SIGINT` = 2

`Ctrl+C` normally sends `SIGINT` to the foreground process.

---

# 46. `jobs` — Shell Jobs

`jobs` shows jobs started by the current shell.

Example:

```bash
sleep 100
```

Normally this runs in the foreground.

Press:

```text
Ctrl+Z
```

to suspend it.

Then:

```bash
jobs
```

You may see:

```text
[1]+  Stopped  sleep 100
```

---

# 47. Background Jobs

Run a command in the background using `&`:

```bash
sleep 100 &
```

Then:

```bash
jobs
```

You may see:

```text
[1]+  Running  sleep 100 &
```

---

## Bring a job to foreground

```bash
fg
```

or:

```bash
fg %1
```

---

## Continue in background

After `Ctrl+Z`:

```bash
bg
```

---

# 48. Process vs Program

An interview-friendly explanation:

### Program

A program is a passive set of instructions stored on disk.

### Process

A process is a running instance of a program.

For example:

```text
program: python
process: python app.py currently running
```

The same program can have multiple processes.

---

# 49. Networking

Commands:

- `curl`
- `wget`
- `ping`
- `ssh`
- `netstat` / `ss`

---

# 50. Basic Networking Concepts

Before commands, understand these terms:

### IP address

Identifies a network interface/host on an IP network.

Examples:

```text
192.168.1.10
```

IPv6 examples:

```text
2001:db8::1
```

### Port

A logical endpoint used by network services.

Examples:

```text
80   HTTP
443  HTTPS
22   SSH
```

A server can have many ports associated with different services.

### Protocol

Rules for communication.

Examples:

- HTTP
- HTTPS
- SSH
- TCP
- UDP
- ICMP

---

# 51. `ping`

`ping` tests reachability using ICMP echo messages in common cases.

Example:

```bash
ping google.com
```

Stop with:

```text
Ctrl+C
```

---

## Send a limited number of packets

Linux:

```bash
ping -c 4 google.com
```

`-c 4` means send four echo requests.

---

## Important interview nuance

A failed `ping` does **not necessarily mean the host is down**.

ICMP may be blocked by:

- firewall
- security policy
- network configuration

Therefore:

> `ping` tests ICMP reachability, not whether a specific application service is working.

---

# 52. `curl`

`curl` transfers data using URLs and supports many protocols.

For web development, the most common use is HTTP/HTTPS requests.

## GET request

```bash
curl https://example.com
```

---

## Show response headers

```bash
curl -I https://example.com
```

---

## Follow redirects

```bash
curl -L https://example.com
```

---

## Download response to a file

```bash
curl -o page.html https://example.com
```

---

## Send a POST request

Example:

```bash
curl -X POST https://example.com/api/users
```

The real API may also require a request body and headers.

Example JSON body:

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{"name":"Alice"}' \
  https://example.com/api/users
```

---

# 53. Why `curl` Matters for Developers

It is useful for:

- testing APIs
- debugging HTTP requests
- checking servers
- downloading data
- automation
- CI/CD scripts

Example:

```bash
curl http://localhost:8080/health
```

This is a very common developer workflow.

---

# 54. `wget`

`wget` is primarily a command-line downloader.

Example:

```bash
wget https://example.com/file.zip
```

It downloads the file.

---

## Rename the output

```bash
wget -O app.zip https://example.com/download
```

---

# 55. `curl` vs `wget`

Simple interview answer:

### `curl`

Excellent for:

- APIs
- HTTP requests
- debugging
- sending custom headers/data
- interacting with web services

### `wget`

Excellent for:

- downloading files
- recursive website/file downloading
- straightforward downloads

They overlap, but their typical workflows differ.

---

# 56. `ssh`

SSH means:

> Secure Shell

It provides secure remote access to another machine over a network.

Basic syntax:

```bash
ssh username@hostname
```

Example:

```bash
ssh alice@192.168.1.20
```

---

# 57. How SSH Works at a High Level

You have:

```text
Your computer
     |
     | encrypted SSH connection
     v
Remote server
```

You authenticate using something such as:

- password
- SSH key pair

SSH commonly uses port:

```text
22
```

unless the server is configured differently.

---

# 58. SSH Key Authentication

You may generate a key pair with:

```bash
ssh-keygen
```

This creates:

- private key
- public key

The key pair concept is:

```text
private key -> keep secret
public key  -> can be placed on the server
```

Never casually share your private SSH key.

---

# 59. `ssh` with a Different Port

```bash
ssh -p 2222 user@server
```

`-p` specifies the remote SSH port.

---

# 60. `netstat` and `ss`

These commands help inspect network sockets and connections.

`netstat` is older and may not be installed on modern distributions.

`ss` is the modern common replacement.

---

# 61. `ss`

## Show listening TCP/UDP sockets

```bash
ss -tuln
```

Common options:

- `-t` = TCP
- `-u` = UDP
- `-l` = listening
- `-n` = don't resolve service names/hostnames; show numeric values

---

## Show processes with sockets

Depending on permissions:

```bash
sudo ss -tulpn
```

This can help answer:

> Which process is listening on port 8080?

---

# 62. `netstat`

Older common command:

```bash
netstat -tuln
```

If unavailable, use:

```bash
ss -tuln
```

For placements, remember:

> `ss` is generally preferred on modern Linux systems.

---

# 63. System Management

Commands:

- `systemctl`
- `journalctl`
- `apt`

These are especially important when working with Linux servers.

---

# 64. What Is a Service?

A service is a background program that provides some functionality.

Examples:

- web server
- database server
- SSH server
- background application

Examples of service names you may encounter:

```text
ssh
nginx
apache2
mysql
postgresql
```

The exact service name depends on the software and distribution.

---

# 65. `systemctl`

`systemctl` is the main command-line interface for controlling and inspecting services managed by **systemd**.

Systemd is an init and service-management system used by many Linux distributions.

---

# 66. Check Service Status

Example:

```bash
systemctl status ssh
```

You may see:

```text
Active: active (running)
```

This tells you whether the service is running and provides recent details.

---

# 67. Start a Service

```bash
sudo systemctl start nginx
```

Starts the service now.

---

# 68. Stop a Service

```bash
sudo systemctl stop nginx
```

Stops it now.

---

# 69. Restart a Service

```bash
sudo systemctl restart nginx
```

Stops and starts it again.

Useful after configuration changes.

---

# 70. Reload vs Restart

Some services support:

```bash
sudo systemctl reload nginx
```

A reload asks the service to reread configuration without a full stop/start when supported.

General idea:

- `restart` = restart process/service
- `reload` = reload configuration if supported

---

# 71. Enable a Service at Boot

```bash
sudo systemctl enable nginx
```

This configures the service to start automatically during boot.

---

# 72. Disable at Boot

```bash
sudo systemctl disable nginx
```

---

# 73. Check Whether Enabled

```bash
systemctl is-enabled nginx
```

---

# 74. Check Whether Active

```bash
systemctl is-active nginx
```

---

# 75. `journalctl` — View Logs

`journalctl` queries the **systemd journal**.

Example:

```bash
journalctl
```

This can show system/service logs.

---

## Show logs for a service

```bash
journalctl -u nginx
```

`-u` means unit.

---

## Follow logs live

```bash
journalctl -u nginx -f
```

This is similar to watching logs as they arrive.

Press:

```text
Ctrl+C
```

to stop.

---

## Show recent logs

```bash
journalctl -u nginx -n 50
```

Shows the latest 50 entries.

---

## Logs since a time

Example:

```bash
journalctl -u nginx --since "1 hour ago"
```

---

# 76. `systemctl` + `journalctl` Debugging Pattern

Suppose a server application is not working.

A useful workflow is:

```bash
systemctl status myservice
```

Then:

```bash
journalctl -u myservice -n 100
```

Then perhaps:

```bash
journalctl -u myservice -f
```

This is a practical production-debugging pattern.

---

# 77. `apt` — Package Management

On Debian/Ubuntu systems, `apt` is used to manage software packages.

A package is a bundle containing software and metadata.

Examples:

```bash
git
curl
python3
nginx
htop
tree
```

---

# 78. Update Package Information

```bash
sudo apt update
```

This refreshes the package index.

Important:

> `apt update` does not normally upgrade installed packages.

It refreshes information about available packages.

---

# 79. Upgrade Installed Packages

```bash
sudo apt upgrade
```

This upgrades installed packages where appropriate.

Common sequence:

```bash
sudo apt update
sudo apt upgrade
```

---

# 80. Install a Package

```bash
sudo apt install tree
```

Multiple packages:

```bash
sudo apt install git curl wget
```

---

# 81. Remove a Package

```bash
sudo apt remove tree
```

---

## Remove package and related configuration files

```bash
sudo apt purge tree
```

The exact cleanup behavior depends on package state; `purge` generally removes package configuration files as well.

---

# 82. Search for Packages

```bash
apt search nginx
```

---

# 83. Show Package Information

```bash
apt show nginx
```

---

# 84. Why `sudo`?

`sudo` means running a command with elevated privileges, normally as root according to the system's sudo policy.

Example:

```bash
sudo apt install nginx
```

Installing software changes system state, so elevated privileges are usually required.

You will also see:

```bash
sudo systemctl restart nginx
```

---

# 85. Root User

The root user is the superuser on traditional Unix/Linux systems.

Root has very broad control over the machine.

Normal users should avoid using root unnecessarily.

Better:

```bash
sudo specific-command
```

instead of staying permanently logged in as root.

---

# 86. Essential Shell Concepts

These are not explicitly in your list, but they are necessary to understand Linux command-line work.

---

# 87. Pipes: `|`

A pipe sends output from one command to another.

Example:

```bash
ps aux | grep nginx
```

Think:

```text
command 1 output
       |
       v
command 2 input
```

You can chain multiple commands:

```bash
cat application.log | grep ERROR | less
```

---

# 88. Redirection: `>`, `>>`, `<`

## Output overwrite

```bash
echo "hello" > file.txt
```

## Output append

```bash
echo "world" >> file.txt
```

## Input redirection

```bash
command < input.txt
```

---

# 89. Standard Streams

Many Linux programs work with three standard streams:

### stdin

Standard input.

File descriptor:

```text
0
```

### stdout

Standard output.

File descriptor:

```text
1
```

### stderr

Standard error.

File descriptor:

```text
2
```

This is an important interview topic.

---

# 90. Redirect stderr

Example:

```bash
command 2> errors.txt
```

Redirects standard error to `errors.txt`.

Redirect stdout:

```bash
command > output.txt
```

Redirect both in Bash:

```bash
command > output.txt 2>&1
```

Modern Bash also supports:

```bash
command &> output.txt
```

---

# 91. Command Chaining

## `&&`

Run the second command only if the first succeeds.

```bash
mkdir test && cd test
```

---

## `;`

Run the second command regardless of whether the first succeeded.

```bash
command1; command2
```

---

## `||`

Run the second command if the first fails.

```bash
command1 || echo "failed"
```

---

# 92. Exit Status

Commands generally return an exit status.

Convention:

```text
0 = success
non-zero = failure/error
```

Check the previous command's exit status:

```bash
echo $?
```

Example:

```bash
ls
echo $?
```

If `ls` succeeded, you will normally get:

```text
0
```

---

# 93. Environment Variables

Environment variables are named values available to processes.

View one:

```bash
echo $HOME
```

Examples:

```bash
echo $PATH
echo $USER
echo $SHELL
```

---

# 94. `PATH`

`PATH` tells the shell where to look for executable commands.

Check it:

```bash
echo $PATH
```

Example conceptually:

```text
/usr/local/bin:/usr/bin:/bin
```

When you type:

```bash
python3
```

the shell searches directories in `PATH` for an executable with that name.

---

# 95. Command Location

Use:

```bash
which python3
```

or on many systems:

```bash
command -v python3
```

This helps determine which executable will be used.

---

# 96. Wildcards

The shell supports patterns called globbing.

## `*`

Matches zero or more characters.

Example:

```bash
ls *.txt
```

Matches:

```text
a.txt
notes.txt
report.txt
```

## `?`

Matches one character.

Example:

```bash
ls file?.txt
```

Could match:

```text
file1.txt
fileA.txt
```

---

# 97. Quoting

Spaces can have special meaning to the shell.

This:

```bash
touch my file.txt
```

is interpreted as multiple arguments.

Use quotes:

```bash
touch "my file.txt"
```

Single quotes:

```bash
echo 'hello $USER'
```

Double quotes:

```bash
echo "hello $USER"
```

Single quotes generally prevent variable expansion, while double quotes allow it.

---

# 98. `man` — Read Command Documentation

This command is worth learning even though it was not in the initial checklist.

Example:

```bash
man ls
```

You can read the manual page for `ls`.

Search within a man page using:

```text
/
```

Quit with:

```text
q
```

Also useful:

```bash
ls --help
```

---

# 99. Practical Workflow: Find a File

Problem:

> You know the filename but not where it is.

Use:

```bash
find . -name "config.yaml"
```

If you need to search the entire filesystem and have permission:

```bash
sudo find / -name "config.yaml" 2>/dev/null
```

`2>/dev/null` hides permission-denied errors from stderr.

---

# 100. Practical Workflow: Find an Error in Logs

Suppose:

```text
application.log
```

Search:

```bash
grep -i "error" application.log
```

With line numbers:

```bash
grep -in "error" application.log
```

For live service logs:

```bash
journalctl -u myservice -f
```

---

# 101. Practical Workflow: Find Which Process Uses a Port

Suppose your application should run on port `8080`.

First:

```bash
ss -tulpn | grep 8080
```

With sufficient privileges:

```bash
sudo ss -tulpn | grep 8080
```

This may show the process listening on that port.

---

# 102. Practical Workflow: Service Not Starting

Run:

```bash
systemctl status myservice
```

Then:

```bash
journalctl -u myservice -n 100
```

Look for:

- configuration errors
- missing files
- permission errors
- port conflicts
- dependency failures
- invalid environment variables

Then correct the problem and restart:

```bash
sudo systemctl restart myservice
```

---

# 103. Practical Workflow: SSH Into a Server

```bash
ssh user@server-ip
```

After connecting:

```bash
pwd
ls
```

Inspect a service:

```bash
systemctl status nginx
```

Inspect logs:

```bash
journalctl -u nginx -n 50
```

Check ports:

```bash
ss -tulpn
```

This small set of commands is already enough for basic server troubleshooting.

---

# 104. Command Cheat Sheet

## Navigation

```bash
pwd
ls
ls -la
cd directory
cd ..
cd ~
cd -
tree
tree -L 2
find . -name "file.txt"
find . -type f -name "*.log"
```

## Files

```bash
cp source dest
cp -r dir1 dir2
mv old new
rm file
rm -r directory
chmod 755 script.sh
chmod +x script.sh
chown user:group file
ln -s target link
```

## Text

```bash
cat file.txt
less file.log
grep "ERROR" file.log
grep -rin "error" .
sed 's/old/new/g' file.txt
awk '{print $1}' file.txt
```

## Processes

```bash
ps
ps aux
ps -ef
top
htop
kill PID
kill -9 PID
jobs
fg
bg
```

## Networking

```bash
curl https://example.com
curl -I https://example.com
wget https://example.com/file.zip
ping -c 4 example.com
ssh user@host
ss -tuln
sudo ss -tulpn
```

## System

```bash
systemctl status nginx
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl enable nginx
journalctl -u nginx
journalctl -u nginx -f
sudo apt update
sudo apt upgrade
sudo apt install package
sudo apt remove package
```

---

# 105. Most Important Placement Questions

## Q1. What is Linux?

A good answer:

> Linux is an open-source kernel. Linux distributions combine the kernel with system utilities, libraries, package management, and other software to form complete operating systems.

---

## Q2. What is a shell?

> A shell is a command interpreter that lets users interact with the operating system by executing commands and scripts.

---

## Q3. What is the difference between absolute and relative paths?

> An absolute path starts from the root directory `/`, while a relative path is interpreted relative to the current working directory.

Example:

```text
Absolute:
/home/user/project

Relative:
project
```

---

## Q4. What does `chmod 755` mean?

Calculate:

```text
7 = rwx
5 = r-x
5 = r-x
```

Therefore:

```text
owner  = rwx
group  = r-x
others = r-x
```

---

## Q5. What is the difference between `chmod` and `chown`?

> `chmod` changes permissions. `chown` changes ownership.

---

## Q6. What is the difference between `cp` and `mv`?

> `cp` copies data to another location, while `mv` moves or renames a file or directory.

---

## Q7. What is the difference between `grep` and `find`?

> `find` searches for files/directories based on attributes such as name, type, or size. `grep` searches for matching text in file contents or command output.

---

## Q8. What is a process?

> A process is a running instance of a program, with its own process-related resources such as a PID, memory mappings, and open file descriptors.

---

## Q9. What is PID?

> PID stands for Process ID. It is an identifier assigned to a process.

---

## Q10. Difference between `kill` and `kill -9`?

> `kill PID` normally sends SIGTERM, asking the process to terminate gracefully. `kill -9 PID` sends SIGKILL, which cannot be caught or ignored by the target process.

---

## Q11. What is SSH?

> SSH is a secure protocol for remote login and command execution over a network.

---

## Q12. What is the difference between `curl` and `wget`?

> `curl` is commonly used for making and inspecting network requests, especially APIs, while `wget` is commonly used for downloading files. Their capabilities overlap.

---

## Q13. What does `systemctl` do?

> `systemctl` is used to inspect and control services managed by systemd.

---

## Q14. What does `journalctl` do?

> `journalctl` queries and displays logs stored by the systemd journal.

---

## Q15. Difference between `apt update` and `apt upgrade`?

> `apt update` refreshes package metadata. `apt upgrade` upgrades installed packages using the updated package information.

---

# 106. Commands You Should Memorize First

Do not try to memorize every option immediately.

Start with these:

```bash
pwd
ls
cd
cd ..
cd ~
cp
mv
rm
chmod
chown
cat
less
grep
ps
top
kill
jobs
curl
wget
ping
ssh
ss
systemctl
journalctl
apt
```

Then learn the common options:

```bash
ls -la
cp -r
rm -r
chmod 755
grep -i
grep -r
ps aux
kill -9
ss -tuln
systemctl status
journalctl -u
apt update
apt install
```

---

# 107. A 7-Day Linux Placement Practice Plan

## Day 1 — Terminal and Navigation

Practice:

```bash
pwd
ls
ls -la
cd
cd ..
cd ~
tree
```

Create directories and move between them.

Goal:

> Become comfortable moving around Linux without a graphical file manager.

---

## Day 2 — Files and Permissions

Practice:

```bash
cp
mv
rm
chmod
chown
ln
```

Create files and experiment with:

```bash
chmod 644 file.txt
chmod 755 script.sh
chmod 600 secret.txt
```

Use:

```bash
ls -l
```

to inspect the results.

---

## Day 3 — Text Processing

Practice:

```bash
cat
less
grep
sed
awk
```

Create a fake log file:

```text
INFO User logged in
ERROR Database connection failed
INFO Request completed
ERROR Timeout
```

Try:

```bash
grep "ERROR" app.log
grep -n "ERROR" app.log
sed 's/ERROR/WARN/g' app.log
awk '{print $1, $2}' app.log
```

---

## Day 4 — Processes

Practice:

```bash
ps
ps aux
top
htop
kill
jobs
fg
bg
```

Run:

```bash
sleep 200 &
```

Then:

```bash
jobs
```

Find the process:

```bash
ps aux | grep sleep
```

Terminate it:

```bash
kill PID
```

---

## Day 5 — Networking

Practice:

```bash
ping -c 4 google.com
curl https://example.com
wget https://example.com
ss -tuln
```

Understand:

- IP
- port
- TCP
- UDP
- HTTP
- HTTPS
- SSH

---

## Day 6 — Services and Packages

Practice:

```bash
systemctl status ssh
journalctl -u ssh -n 50
apt search tree
sudo apt install tree
```

Learn:

- service
- daemon
- systemd
- journal
- package
- repository

---

## Day 7 — Interview Practice

Without looking at notes, explain:

1. Linux vs shell
2. absolute vs relative path
3. `chmod 755`
4. owner/group/others
5. process vs program
6. PID
7. `SIGTERM` vs `SIGKILL`
8. `grep` vs `find`
9. `curl` vs `wget`
10. `systemctl` vs `journalctl`
11. `apt update` vs `apt upgrade`
12. symbolic link vs hard link

Then complete a mini troubleshooting exercise:

```text
A web application is not accessible.
```

Try to reason through:

```bash
systemctl status myservice
journalctl -u myservice -n 100
ss -tulpn
ps aux
curl http://localhost:8080
```

---

# 108. Mini Project: Linux Server Simulation

Create a directory:

```bash
mkdir linux-practice
cd linux-practice
```

Create files:

```bash
touch app.log users.txt config.txt
```

Put sample content into `users.txt`:

```text
Alice Developer
Bob Tester
Charlie Admin
```

Then practice:

```bash
cat users.txt
awk '{print $1}' users.txt
grep "Admin" users.txt
cp users.txt users_backup.txt
mv config.txt app_config.txt
ls -la
find . -type f
```

Create a script:

```bash
echo '#!/bin/bash' > hello.sh
echo 'echo "Hello Linux"' >> hello.sh
```

Make it executable:

```bash
chmod +x hello.sh
```

Run:

```bash
./hello.sh
```

Check:

```bash
ls -l hello.sh
```

This small exercise connects several concepts together.

---

# 109. A Mental Model for Linux

Think of Linux as several layers:

```text
Applications
     |
     v
Shell / commands
     |
     v
Libraries / system calls
     |
     v
Linux kernel
     |
     v
Hardware
```

For example:

```bash
curl https://example.com
```

ultimately relies on the kernel to perform networking operations with the hardware.

You do not need to understand every kernel detail for a placement, but understanding this layered model helps a lot.

---

# 110. The Most Important Ideas to Truly Understand

Do not focus only on memorizing syntax.

Make sure you genuinely understand these:

### Filesystem

```text
/
├── home
├── etc
├── var
├── tmp
└── usr
```

### Paths

```text
/
.
..
~
```

### Permissions

```text
r = 4
w = 2
x = 1
```

### Ownership

```text
owner
group
others
```

### Processes

```text
program -> process -> PID
```

### Signals

```text
SIGTERM
SIGKILL
SIGINT
```

### Networking

```text
host -> IP -> port -> service
```

### Services

```text
systemctl -> control service
journalctl -> inspect logs
```

### Packages

```text
apt update  -> refresh package metadata
apt install -> install software
apt upgrade -> upgrade installed packages
```

---

# 111. Final Placement Checklist

You are in a good position for basic Linux questions when you can comfortably do all of the following without looking up every command:

## Navigation

- [ ] Explain current working directory
- [ ] Use `pwd`
- [ ] Use `ls`
- [ ] Use `cd`
- [ ] Explain `.`, `..`, `~`, `/`
- [ ] Use `tree`
- [ ] Use `find`

## Files

- [ ] Copy files with `cp`
- [ ] Move/rename with `mv`
- [ ] Remove files/directories with `rm`
- [ ] Explain recursive operations
- [ ] Explain `chmod`
- [ ] Calculate permission numbers
- [ ] Explain owner/group/others
- [ ] Use `chown`
- [ ] Explain symbolic links and hard links

## Text

- [ ] Read files with `cat`
- [ ] Read large files with `less`
- [ ] Search text with `grep`
- [ ] Perform basic substitutions with `sed`
- [ ] Extract fields with `awk`
- [ ] Explain pipes

## Processes

- [ ] Explain what a process is
- [ ] Find processes with `ps`
- [ ] Monitor with `top`
- [ ] Use `htop`
- [ ] Explain PID
- [ ] Explain `SIGTERM`
- [ ] Explain `SIGKILL`
- [ ] Use `kill`
- [ ] Understand shell jobs with `jobs`, `fg`, and `bg`

## Networking

- [ ] Explain IP address
- [ ] Explain ports
- [ ] Use `ping`
- [ ] Use `curl`
- [ ] Use `wget`
- [ ] Explain SSH
- [ ] Understand public/private SSH keys
- [ ] Use `ss`
- [ ] Know that `netstat` is older and `ss` is commonly preferred

## System

- [ ] Explain systemd at a high level
- [ ] Use `systemctl status`
- [ ] Start/stop/restart services
- [ ] Enable services at boot
- [ ] Read logs with `journalctl`
- [ ] Follow logs live
- [ ] Explain `apt update`
- [ ] Explain `apt upgrade`
- [ ] Install/remove packages with `apt`

---

# 112. One-Page Mental Cheat Sheet

```text
WHERE AM I?
pwd

WHAT IS HERE?
ls
ls -la

MOVE
cd folder
cd ..
cd ~
cd -

FIND A FILE
find . -name "name"

COPY
cp file destination
cp -r directory destination

MOVE / RENAME
mv old new

DELETE
rm file
rm -r directory

PERMISSIONS
chmod 755 file
chmod +x script.sh

OWNERSHIP
chown user:group file

LINK
ln -s target link

READ
cat file
less file

SEARCH TEXT
grep "text" file

TRANSFORM
sed 's/old/new/g' file

FIELDS
awk '{print $1}' file

PROCESSES
ps aux
top
htop

STOP PROCESS
kill PID
kill -9 PID

SHELL JOBS
jobs
fg
bg

HTTP / API
curl URL
curl -I URL

DOWNLOAD
wget URL

REACHABILITY
ping -c 4 host

REMOTE LOGIN
ssh user@host

NETWORK PORTS
ss -tuln

SERVICE
systemctl status service
systemctl start service
systemctl restart service

LOGS
journalctl -u service
journalctl -u service -f

PACKAGES
sudo apt update
sudo apt install package
sudo apt upgrade
```

---

# 113. Final Advice for Placements

Do not try to become a Linux administrator before your placement interviews.

For most entry-level software roles, your highest-value goal is:

> **Understand what each command does, why you would use it, and how to combine a few commands to solve a practical problem.**

For example, knowing:

```bash
systemctl status
journalctl -u
ss -tulpn
ps aux
grep
```

and understanding when to use each one is much more valuable than memorizing dozens of obscure command options.

A strong beginner should be able to look at a problem such as:

```text
"My application is not reachable."
```

and start investigating logically:

```text
1. Is the service running?
2. What do the service logs say?
3. Is the process running?
4. Is the expected port listening?
5. Does localhost respond?
6. Is the issue application-level or network-level?
```

That troubleshooting mindset is one of the most useful Linux skills you can develop for placements and real-world software engineering.
