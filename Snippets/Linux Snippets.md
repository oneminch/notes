## File System

- **`/`**
    - Contains the entire filesystem hierarchy.
- **`/bin`**
    - Contains essential binary executables and command-line tools, such as:
        - Shell interpreters (e.g., bash, sh)
        - Basic system utilities (e.g., ls, cp, mv, rm)
        - Text processing tools (e.g., grep, sed, awk)
- **`/etc`**
    - Stores system-wide configuration files, including:
        - Network configuration (e.g., /etc/network/interfaces)
        - User and group information (e.g., /etc/passwd, /etc/group)
        - System startup scripts (e.g., /etc/init.d)
        - Application configurations (e.g., /etc/apache2/apache2.conf)
- **`/home`**
    - Contains user home directories, which typically include:
        - Personal files and documents
        - User-specific configuration files (e.g., .bashrc, .profile)
        - Application data (e.g., browser bookmarks, email client data)
- **`/opt`**
    - Houses optional or third-party software packages, such as:
        - Proprietary applications (e.g., Google Earth)
        - Custom-compiled software
        - Large software suites (e.g., IBM WebSphere)
- **`/tmp`**
    - Provides temporary storage for various processes and users:
        - Temporary files created by applications
        - Session data
        - Cached information
- **`/usr`**
    - Contains user-related programs, libraries, and data:
        - Additional binaries and executables (/usr/bin)
        - System administration commands (/usr/sbin)
        - Libraries (/usr/lib)
        - Header files (/usr/include)
        - Documentation (/usr/share/doc)
- **`/var`**
    - Stores variable data that changes during system operation:
        - Log files (/var/log)
        - Spool directories for print jobs (/var/spool)
        - Temporary files that persist between reboots (/var/tmp)
        - Website content for web servers (/var/www)

## Commands

### File System

- `rm` - remove files or directories
    - `rm -rf dir1 dir2 dir3`
- `touch filename` - create a file
- `ls` - list directory contents
    - `ls -a` - list all contents
    - `ls -l` - list all contents with detailed view
- `cat` - create, view and concatenate files
    - `cat > file` - create new file
    - `cat srcfile > destfile` - copy `srcfile` into `destfile`
- `less` - views the content of a file one screen at a time, allowing for scrolling.
- `mv src [src2 src3 ...] dir` - move source files to directory
    - `mv srcdir existing_dir` - move folder (`existing_dir/srcdir`)
    - `mv old_dir new_dir` - rename folder
    - `mv srcfile destfile` - rename source to destination
- `cp` - copy files or directories
    - By default, existing files are overwritten without confirmation.
    - `cp file1 file2`
    - `cp -r dir1 dir2` - copy all contents of `dir1` to `dir2` (-> `dir2/dir1`)
    - `cp -i file1 file2` - prompt for confirmation before overwriting
- `pwd` - present working directory
- `diff file1.txt file2.txt` - compare file content differences
- `grep [OPTION...] PATTERNS [FILE...]` - search and print lines that match patterns
    - `-r` allows recursive searching through directories.
- `find DIR -name PATTERN` - search for file in a directory hierarchy
- `chmod` - change/modify file permissions
- `sort file` - sort lines of text files

- **Piping** (`|`) - take command and feed it to another
- **Redirection** (`>`) - write output to a file
    - e.g. `scoop list > scoop-installs.txt`

- `touch file-{m..n}.txt` - Create numbered files (from `m` to `n`)
- `mkdir dir-{m..n}` - Create numbered directories (from `m` to `n`)

### Processes

- `ps` - displays information about currently running processes.
    - `ps aux` - detailed view.
- `kill` - terminates processes by their PID (Process ID).
    - `kill -9` - forceful termination.
- `top` - provides a dynamic, real-time view of running processes and system resource usage.
- `htop` - interactive process viewer, with more user-friendly interface than `top`.

### Networking

- `ping` - tests connectivity to another host.
- `curl` - transfers data from or to a server, supporting various protocols. Useful for testing APIs.
- `scp` - securely copies files between hosts on a network.

### System Info

- `df -h` - displays disk space usage in a human-readable format.
- `du -sh` - summarizes disk usage of a directory.
- `free -m` - shows memory usage.
- `uname -a` - displays system information including kernel version and architecture.

### Miscellaneous

- `man` - user manual for any command
- `history` - shows a list of previously executed commands, which can be useful for recalling complex commands.
- `sudo` - runs commands as the root user
    - `-u <user> <command>` - specifies that a command should be run as the specified user.
        - e.g. `sudo -u postgres psql`, `sudo -u nginx nginx -t`
    - `systemctl [start|stop|status] <service>` - newer and preferred method of managing services on systems that use `systemd`.
        - e.g. `sudo systemctl start postgresql`
    - `service <service> [start|stop|status]` - An older, more traditional command available on most Linux distributions for backward compatibility.
        - A high-level wrapper that can work across different `init` systems, including `systemd`.
        - On systems using `systemd`, the `service` command often redirects to `systemctl`.
        - e.g. `sudo service postgresql start`
    - `sudo su` - allows a user with `sudo` privileges to switch to the root user account without needing to know the root password.
        - Effectively gives the user a root shell with full system access.
        - Generally discouraged in favor of `sudo -i` or `sudo -s` for security reasons, as these methods provide better logging and control over elevated privileges.
    - `apt` - CLI for managing packages
        - `install` - install a package
        - `update` - checks to see which packages can be updated.
        - `upgrade` - performs the update for packages that can be updated

## Bash Scripting

- Number of arguments: `$#`
- All positional arguments (as a single word): `$*`
- All positional arguments (as separate strings): `$@`
- List array elements: `${array[*]}`

## Vim

- `vim filename` - edit a file via Vim
    - `i` - used to enter input mode.
    - `l` - moves the cursor to the right.
    - `h` - moves the cursor to the left.
    - `k` - moves the cursor up.
    - `j` - moves the cursor down.
    - `w` - place cursor after a word
    - `b` - jump to beginning of a word
    - `dd` - delete a line
    - `u` - undo a command
    - `dw` - delete word where cursor is located
    - `esc` - enter command mode
    - `:wq` - save and quit vim
    - `.` (period) - repeat a previous command
