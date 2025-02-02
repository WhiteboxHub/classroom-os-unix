# UNIX - OS

UNIX is a multi-user, multitasking operating system (OS) that allows users to interact directly with a computer.

---

## Different Types of Unix

### 1. System V (SVR4)
- **Commercial Unix standard (AT&T)**
- Used in **IBM AIX, Solaris, HP-UX**
- Uses `pkgadd` for package management

### 2. BSD (Berkeley Software Distribution)
- **Open-source Unix derivative**
- Examples: **FreeBSD, OpenBSD, NetBSD**
- Uses `pkg` or `ports` system

### 3. Linux (Unix-like)
- **Open-source and community-driven**
- Popular distros: **Ubuntu, CentOS, Arch, Alpine**
- Package managers: **apt, yum, dnf, pacman**

### 4. macOS (Darwin)
- **Unix-based system from Apple**
- Uses `brew` and `port` for package management

---

## Docker Setup for Different Unix Variants

### Build Docker Images
```sh
docker build -t my_ubuntu -f Dockerfile.ubuntu .
docker build -t my_centos -f Dockerfile.centos .
docker build -t my_alpine -f Dockerfile.alpine .
docker build -t my_openbsd -f Dockerfile.openbsd .
```

### Run Containers
```sh
docker run -it --rm my_ubuntu /bin/bash
docker run -it --rm my_centos /bin/bash
docker run -it --rm my_alpine /bin/sh
docker run -it --rm my_openbsd /bin/sh
```

### Start Containers with Package Management
```sh
docker run -dit --name ubuntu_container my_ubuntu /bin/bash
apt update && apt install -y vim
dpkg -i package.deb  # Manual installation


docker run -dit --name centos_container my_centos /bin/bash
yum install -y vim
rpm -ivh package.rpm  # Manual installation


docker run -dit --name alpine_container my_alpine /bin/sh
apk update && apk add vim


docker run -dit --name openbsd_container my_openbsd /bin/sh
pkg_add -r vim
```

---

## Basic Unix Commands

### System Information
```sh
uname -a   # Display system information
hostname   # Show hostname
whoami     # Print current user
uptime     # Show system uptime
```

### File and Directory Management
#### Listing Files
```sh
ls           # List files in the current directory
ls -l        # Detailed list with permissions
ls -a        # Show hidden files
ls -lh       # Human-readable file sizes
```

#### Creating and Removing Files/Directories
```sh
touch myfile.txt   # Create an empty file
mkdir mydir        # Create a directory
rm myfile.txt      # Remove a file
rmdir mydir        # Remove an empty directory
rm -rf mydir       # Remove a directory with all its contents
```

#### Copying and Moving Files
```sh
cp file1.txt file2.txt   # Copy file1 to file2
mv file1.txt mydir/      # Move file to a directory
mv oldname.txt newname.txt # Rename a file
```

#### Viewing File Contents
```sh
cat myfile.txt   # Display file content
less myfile.txt  # View file content page-by-page
head -n 5 myfile.txt  # Show first 5 lines
tail -n 5 myfile.txt  # Show last 5 lines
```

---

## File Permissions
### Viewing Permissions
```sh
ls -l  # Shows permissions
```

### Changing Permissions
```sh
chmod 755 script.sh  # Assign read/write/execute to owner, read/execute to others
chmod +x script.sh   # Add execute permission
```

### Changing Ownership
```sh
chown user:user file.txt  # Change ownership of a file
```

---

## Process Management
```sh
ps aux      # Show running processes
top         # Monitor system processes (press 'q' to exit)
kill <PID>  # Kill a process using its Process ID (PID)
```

### Find a process by name
```sh
pgrep bash  # Find process ID of bash
```

### Stop a running process
```sh
kill -9 <PID>  # Force kill a process
```

---

## Disk Usage
```sh
df -h      # Show disk usage in human-readable format
du -sh *   # Show size of all files in the current directory
```

---

## Networking Commands
```sh
ifconfig      # Show network configuration (use `ip a` in newer versions)
ping google.com  # Test internet connectivity
curl ifconfig.me  # Get public IP address
netstat -tulnp  # Show open network ports
```

---

## Searching for Files
```sh
find / -name "file.txt"  # Find a file by name
grep "pattern" file.txt  # Search for a pattern in a file
```

---

## Using vi Editor
### Install vim:
```sh
apt update && apt install -y vim
```
### Opening a File in vi
```sh
vi myfile.txt
```

---

## Using nano Editor
### Install nano:
```sh
apt install -y nano
```
### Opening a File in nano
```sh
nano myfile.txt
```

---

## Reading System Logs
### Viewing Logs in Ubuntu
Ubuntu uses `systemd-journald` for logging.
```sh
journalctl -xe  # View recent logs
journalctl --since "10 minutes ago"  # Logs from last 10 minutes
```

### Viewing Specific Logs
```sh
cat /var/log/syslog  # System logs
cat /var/log/auth.log  # Authentication logs
cat /var/log/dpkg.log  # Package installation logs
```

### Monitor Logs in Real-Time
```sh
tail -f /var/log/syslog
```

---

## Writing a Basic Shell Script
### Create a simple shell script that prints "Hello, Unix!"
```sh
nano hello.sh
```
Add the following content:
```sh
#!/bin/bash
echo "Hello, Unix!"
```
Save the file and make it executable:
```sh
chmod +x hello.sh
./hello.sh
```

---

## Setting Up a Cron Job
### Understanding cron
Cron jobs allow you to schedule tasks.
```sh
apt install -y cron
service cron start
```

### Add a Cron Job
```sh
crontab -e
```
Add the following line:
```sh
* * * * * /bin/bash /hello.sh >> /var/log/hello.log 2>&1
```

### Verify the Cron Job
```sh
crontab -l
cat /var/log/hello.log
```

---

## File Watcher Example
A file watcher script monitors a directory for changes and logs them.

### Create the Bash Script
```sh
nano /file_watcher.sh
```
Add the script:
```sh
#!/bin/bash
WATCH_DIR="/watched_directory"
LOG_FILE="/var/log/file_watcher.log"
mkdir -p "$WATCH_DIR"
ls -l "$WATCH_DIR" > /tmp/current_state
while true; do
    sleep 10
    ls -l "$WATCH_DIR" > /tmp/new_state
    diff /tmp/current_state /tmp/new_state >> "$LOG_FILE"
    mv /tmp/new_state /tmp/current_state
done
```
Run in the background:
```sh
nohup /file_watcher.sh &
```
Check logs:
```sh
cat /var/log/file_watcher.log
```

## Scheduling the File Watcher with Cron
Instead of running it manually, schedule it to run every minute.

### Edit the cron jobs:
```sh
crontab -e
```

### Add this line to check the directory every minute:
```sh
* * * * * /bin/bash /file_watcher.sh &
```

Save & exit (CTRL + X, Y, Enter).

### Check if the cron job is set:
```sh
crontab -l
```

## Testing the File Watcher
### Create the monitored directory:
```sh
mkdir -p /watched_directory
```

### Add a test file:
```sh
touch /watched_directory/test_file.txt
```

### Modify the file:
```sh
echo "Hello, Unix!" > /watched_directory/test_file.txt
```

### Delete the file:
```sh
rm /watched_directory/test_file.txt
```

### Check the log to see changes:
```sh
cat /var/log/file_watcher.log
```
---

## License
This project is licensed under the MIT License.

## Contributors
- Whitebox Learning
