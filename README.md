# Linux System Administrator Portfolio

## 👤 About Me

Network Engineer with a Master's in Web Science (Information Security). 
Passionate about Linux system administration, cybersecurity, and vulnerability research. 
This portfolio documents my hands-on Linux journey through real-world troubleshooting scenarios and practical labs.
Currently seeking remote opportunities as a Junior System Administrator or NOC Engineer.

---

## 📂 Command Line

### Scenario 1: VirtualBox Shared Folder & Guest Additions Issue

**Date:** 13/8/2026

**Problem:**
While testing Linux commands on a VirtualBox virtual machine, I needed to transfer images and compressed files from my Windows host to test the `file` command with different data types. However, the Drag & Drop feature was disabled.

**Solution:**
1. Installed Guest Additions using:
   ```bash
   sudo apt update && sudo apt install -y build-essential dkms linux-headers-$(uname -r)
   sudo sh /media/$USER/VBox_GAs_*/VBoxLinuxAdditions.run
   ```
2. Enabled Clipboard and Drag & Drop to Bidirectional from VM settings.

3. Encountered a Timeout error when dragging from the VM to the host, so I used Shared Folders as an alternative.

4. Set up a shared folder but faced a Permission Denied error. Solved it by adding the user to the vboxsf group:
```bash
sudo usermod -aG vboxsf $USER
```
5. After reboot, successfully transferred capture.jpg to Ubuntu.

Result:
Ran file capture.jpg and confirmed it was a real image (not just a fake extension), expanding my testing from text files to multimedia files.

Lesson Learned:
Learned how to troubleshoot virtualization issues, find alternative solutions, and the importance of user group management in Linux for access control.

Skills: file, usermod, apt, VirtualBox Guest Additions

### Scenario 2: Hidden Characters in Shell Script
**Date:** 14/8/2026

**Problem:**
I was reviewing a shell script (script.sh) that looked normal but refused to execute properly.

**Solution:**
Used cat -A script.sh to reveal hidden characters. Discovered a ^I (Tab character) instead of a regular space,
 and a ^M (Windows line ending) which causes script failures in Linux.

**Result:**
Identified two hidden causes of the failure and fixed the file. Learned the importance of using 
cat -A to audit sensitive files before execution.

**Skills:** cat -A


### Scenario 3: Recovering Commands & Editing Environment
**Date: 14/8/2026**

**Problem:**
While trying to recall a command I had previously executed to edit ~/.profile, I used history 
!nano but encountered a syntax error:

bash: history: nano: numeric argument required

**Solution:**
Realized that history with ! requires a command number, not a name. Listed the command history, found the correct number
, used !<number> to repeat it, then edited ~/.profile and removed the problematic line.

**Result:**
Successfully tracked previous steps and corrected the user environment, saving time searching for the command again.

**Skills:** history, !, nano, ~/.profile

### Scenario 4: Backup with Preserved Timestamps
**Date:** 14/8/2026

**Problem:**
Needed to backup file3.txt from Documents to Desktop while preserving the original timestamps.

**Solution:**
Used cp -p file3.txt /home/hassan/Desktop, then verified with ls -l which showed the original date/time preserved 
(Aug 14 11:46).

**Result:**
Created an accurate backup preserving original file properties — essential for any administrative task.

**Skills:** cp -p, ls -l

### Scenario 5: Safe File Move with Automatic Backup
**Date:** 16/8/2026

**Problem:**
Needed to move file3.txt to the Pictures folder, which already contained a file with the same name. Wanted to keep the old version as a backup and see the operation details.

**Solution:**
Used mv -v -b file3.txt /home/hassan/Pictures. This created a backup named file3.txt~ before overwriting, and displayed the transfer details. Encountered a path typo (Picture instead of Pictures) and corrected it immediately.

**Result:**
File moved successfully with a backup of the old file preserved. Verified the operation via command history.

**Skills:** mv -v -b

### Scenario 6: Finding Log Files with find -exec
**Date:** 16/8/2026

**Problem:**
Wanted to search for all .log files in the current directory and subdirectories, and display their full details (permissions, size, date) in a single list.

**Solution:**
Used:
```bash
find . -name "*.log" -exec ls -l {} \;
```
Where -exec executes ls -l on each found file.

**Result:**
Got a detailed list of all log files, saving significant manual search time.

**Skills:** find, -exec, ls -l

## 📂 Permissions
### Scenario 7: Setting File Permissions with chmod
**Date:** 27/8/2026

**Problem:**
I have a file on Ubuntu. I want full permissions for myself (user hassan), while other users can only read it.

**Solution:**
```bash
chmod 744 myfile.txt
```
**Result:**
I can modify and read the file, while other users are not allowed to do so.

**Skills:** chmod

### Scenario 8: Changing File Ownership with chown
**Date:** 27/8/2026

**Problem:**
I have a file named hassan.txt owned by user hassan from group hassan. I want to transfer its ownership to another user on the same machine belonging to a different group.

**Solution:**
```bash
sudo chown user2:user2 hassan.txt
```
**Result:**
File ownership changed from hassan:hassan to user2:user2.

**Skills:** chown

### Scenario 9: Creating a Shared Development Folder
**Date:** 27/8/2026

**Problem:**
Need to create a shared folder for all developers with full read/write permissions, while preventing other users from accessing it.

**Solution:**
```bash
mkdir /var/dev_folder
ls -ld /var/dev_folder
sudo chown hassan:sharegroup /var/dev_folder
sudo chmod 770 /var/dev_folder
```
**Result:**
Successfully accessed dev_folder and all its subdirectories from the current user and group members.

**Skills:** mkdir, ls -ld, chown, chmod

### Scenario 10: Troubleshooting Shared Folder Permission Denied
**Date:** 27/8/2026

**Problem:**
While accessing a Shared Folder between Windows and Ubuntu, I got a Permission Denied error when trying to open it from user user2 (recently added), even though it was configured correctly in VirtualBox.

**Solution:**
```bash
ls -ld /media/sf_shared_with_virtualbox
```
Discovered the owner is root and the group is vboxsf, and user hassan is in this group but user2 is not. 
Solved by adding user2 to the vboxsf group:
```bash
sudo usermod -aG vboxsf user2
```
Then logged out and back in (or rebooted) to apply the new permissions.

**Result:**
Successfully accessed the shared folder and read/wrote files in it.

**Skills:** ls -ld, usermod

## 📂 Processes & Services
### Scenario 11: Force Killing a Frozen GUI Application
**Date:** 28/8/2026

**Problem:**
I was working on LibreOffice Calc when it suddenly froze. Clicking the close button (X) did nothing.

**Solution:**
Used:
```bash
ps aux | grep libreoffice
```
Used grep to filter ps aux output and show only LibreOffice processes. Identified the correct 
PID (the one with TTY value ? indicating GUI, not Terminal). Then:
```bash
kill <PID>
```
**Result:**
The frozen program was forcibly closed.

**Skills:** ps aux, grep, kill

### Scenario 12: Job Control & Process States
**Date: 30/8/2026**

**Problem:**
I ran a system-wide grep search:
```bash
sudo grep -r "error" / 2>/dev/null
```
I pressed Ctrl+Z to pause it and return to the terminal, then ran bg %1. The problem: the process returned to the foreground and stopped responding to Ctrl+Z or Ctrl+C.

**Solution:**
Redirected output to a file:
```bash
sudo grep -r "error" / > report.txt 2>/dev/null
```
Then:

- Ctrl+Z → paused the process

- jobs → showed it as stopped

- bg %1 → resumed it in the background

- jobs → showed it as running

- ps aux | grep error → identified PID 1905 with state D (Uninterruptible sleep)

- sudo kill -STOP 1905 → paused it (signal 19)

- sudo kill -CONT 1905 → resumed it

- fg %1 → brought it back to foreground

- sudo kill -15 1905 → terminated it gracefully

**Result:**
Solved the foreground/background issue and practiced full job control and process state tracking.

**Skills:** Ctrl+Z, bg, fg, jobs, ps aux, kill -STOP, kill -CONT, kill -15

### Scenario 13: Adjusting Process Priority with nice
**Date:** 30/8/2026

**Problem:**
The grep search was consuming 13% CPU, negatively affecting other programs.

**Solution:**
```bash
sudo nice -n 15 grep -r "error" / > results.txt 2>/dev/null &
```
Value 15 means low priority, allowing the CPU to handle other tasks smoothly.

**Result:**
CPU usage was reduced without affecting other processes.

**Skills:** nice

### Scenario 14: Managing SSH Service with systemctl
**Date:** 10/9/2026

**Problem:**
SSH service was not running, and remote connection attempts were refused.

**Solution:**
```bash
sudo systemctl status ssh
sudo systemctl start ssh
sudo systemctl enable ssh
sudo ss -tulpn | grep 22
```
**Result:**
SSH is now running and persists after reboot.

**Skills:** systemctl status, systemctl start, systemctl enable, ss

### Scenario 15: Troubleshooting degraded System State
**Date:** 10/9/2026

**Problem:**
Ran systemctl is-system-running → result was degraded (a service failed during boot).

**Solution:**

1. systemctl - -failed → identified vboxadd-service.service

2. sudo systemctl status vboxadd-service.service → saw the error details

3. Tried:
```bash
sudo apt update
sudo apt install build-essential dkms linux-headers-$(uname -r)
sudo /media/cdrom/VBoxLinuxAdditions.run
```
4. Still degraded. Found the issue: old installations conflicting. Solved:
```bash
sudo dpkg - -purge virtualbox-guest-utils virtualbox-guest-x11
sudo apt autoremove -y
sudo systemctl reset-failed vboxadd-service.service
sudo ./VBoxLinuxAdditions.run
sudo reboot
```
5. Verified: ``` bash systemctl is-system-running ``` → running

Result:
System restored from degraded to running.

**Skills:** systemctl is-system-running, systemctl --failed, systemctl status, dpkg --purge, apt autoremove, systemctl reset-failed

## 📂 Networking
### Scenario 16: Diagnosing Routing Issue with ping & ip route
**Date:** 18/9/2026

**Problem:**
In a lab environment: Ubuntu VM (static IP) as a server, Windows laptop as a client. The server could be accessed from the laptop but couldn't reach the internet.

**Solution:**
```bash
ping 8.8.8.8
```
**Result:** From 192.168.43.158 icmp_seq=2 Destination Host Unreachable (ICMP Type 3, Code 1). 
This means the Gateway was wrong
```bash
ip route show
```
Discovered the Gateway was incorrect. Fixed it to the correct mobile hotspot IP (192.168.43.1).

**Result:**
ping 8.8.8.8 started succeeding with ICMP Echo Reply from Google.

**Skills:** ping, ip route show

### Scenario 17: Blocking & Unblocking ICMP with iptables & traceroute
**Date:** 18/9/2026

**Problem:**
Wanted to block ICMP ping requests to the server (a common company policy) and monitor the effect using traceroute.

**Solution:**
On the server:
```bash
sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
```
From the laptop:
```bash
tracert -d 192.168.43.159
```
**Result:**
*** (Request timed out) from the first hop — confirming the packet reached the target but was dropped by the firewall.
Then removed the block:
```bash
sudo iptables -D INPUT -p icmp --icmp-type echo-request -j DROP
```
**Result:**
Successfully blocked and unblocked ICMP, and monitored the effect with traceroute.

**Skills:** iptables, traceroute

### Scenario 18: Port Binding & Web Server Access
**Date:** 20/9/2026

**Problem:**
Started a Python web server on the VM:
```bash
python3 -m http.server 8000 --bind 127.0.0.1
```
Tried to access it from the laptop browser at http://192.168.43.159:8000 → Connection refused.

**Solution:**
```bash
ss -ltn
```
Discovered the server was listening on 127.0.0.1:8000 (localhost only). To accept external connections, it must listen on 0.0.0.0:8000.
```bash
python3 -m http.server 8000
```
Verified:
```bash
ss -ltn
```
Now listening on 0.0.0.0:8000.

**Result:**
Accessed the server from the laptop browser successfully.

**Skills:** ss -ltn, python3 -m http.server, Port Binding

### Scenario 19: DNS Poisoning Detection with /etc/hosts
**Date:** 8/10/2026

**Problem:**
I edited /etc/hosts to map www.google.com to my laptop's IP (192.168.43.76) and forgot about it. Later, Google wouldn't open from the VM or any device using the server as gateway, but worked from mobile.

**Solution:**
```bash
dig google.com
```
Showed the real Google IP (142.250.185.78) because dig queries the external DNS directly.
```bash
getent hosts google.com
```
Showed the wrong IP (my laptop's IP). Removed that line from /etc/hosts.

**Result:**
Google opened successfully again.

Lesson Learned:
This is a form of DNS Poisoning — attackers use /etc/hosts to redirect users to fake sites. To prevent: regularly audit /etc/hosts, use dig and getent hosts to verify, and monitor unauthorized changes.

**Skills:** dig, getent hosts, /etc/hosts, DNS Poisoning

## 🛠️ Technical Skills Summary
| Category | Skills |
| :- - - | :- - - |
| **Command Line** | `ls`, `cd`, `cp`, `mv`, `rm`, `find`, `grep`, `cat`, `nano`, `history`, `file` |
| **Permissions** | `chmod`, `chown`, `chgrp`, `usermod`, `ls -ld` |
| **Processes**	| `ps aux`, `top`, `kill`, `nice`, `jobs`, `bg`, `fg`, `Ctrl+Z` |
| **Services**	| `systemctl`, `journalctl`, `dpkg`, `apt` |
| **Networking** | `ping`, `traceroute`, `ip route`, `ss`, `iptables`, `dig`, `getent` |
| **Other** | `VirtualBox`, `Git`, `Markdown` |

## 📫 Contact
GitHub: [HassanMubarak137](https://github.com/HassanMubarak137)

Email: hassan.mbarak994@gmail.com

## Portfolio Stats
**Total Scenarios:** 19
**Categories:** 4 (Command Line, Permissions, Processes, Networking)
**Last updated:** October 2026
This portfolio is a living document — updated continuously as I learn and practice.
