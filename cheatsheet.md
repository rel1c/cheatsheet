# Linux Cheatsheet
>Disclaimer: This is my own personal Linux cheatsheet. The information here was gathered from my own experiences and many online sources and is not in any way an official guide.

### apt:
```text
update              #update package database to newer packages (hit: same, ign: ignored, get: new)
upgrade             #upgrade packages with available updates
full-upgrade        #works same as above, but will executed needed package removal
install <pkg>       #straight forward, can install multiple packages
remove <pkg>        #removes binaries of a package, leaves configs
purge <pkg>         #removes everything related to package including configs
search <pkg>        #search for packages
show <pkg>          #show info for package
list --upgradable
list --installed
list --all-versions #list packages available for system
autoremove          #removes libs and packages that were once dependencies
```

### chmod _(change file mode bits)_:
```text
owner - group - others
0: no permission
1: execute permission
2: write permission
3: write and execute permissions
4: read permission
5: read and execute permissions
6: read and write permissions
7: read, write and execute permissions
```

### computer information:
```text
nproc             #number of processors
lscpu             #cpu info
lshw              #hardware info (run as root)
lspci             #pci bus info
lsusb             #usb controller info
inxi -Fx          #bash script that fetches hardware details w/ beautified output
lsblk             #block device info
df -h             #disk space of file systems
free -m           #check memory usage
/proc/cpuinfo     #file containing cpu info
/proc/meminfo     #file containing memory info
hdparm -i /dev/   #hard disk information
```

### distribution information:
```text
lab-release -a
cat /etc/*-release
cat /etc/debian_version
cat /issue.net
hostnamectl             #for GNU systemd based distro
```

### disk management:
```text
dd (data define)
  dd bs=## if=<input file path> of=<output file path> status=progress

backup disk as image:
sudo dd if=/dev/sda conv=sync,noerror bs=64K | gzip -c  > /PATH/TO/DRIVE/backup_image.img.gz

fdisk (fixed disks):
  -l              #list disk info
  /PATH/TO/DISK   #edit partitions

  cfdisk (curses fixed disks) #TUI based fdisk
  parted #list and modify partitions
  blkid  #prints block device attributes like uuid and file system type
  mkfs (make file system)   #mkfs.<format> /PATH/TO/PARTITION
```

### kernel:
```text
uname -mrs      #kernel name, version and machine hardware
/proc/version   #file containing kernel version
```

### networking:
```text
a     #protocol (IP or IPv6) address on a device, can also use "address"
r     #routing table entry, can also use "route"
```

### lxd _(linux containers)_:
```text
lxc image list [remote:]                  #list container images
lxc init [repository:][imagename]         #create a container but do not start it
lxc info [remote:]                        #list all containers (running and stopped)
lxc info [remote:][name]                  #get detailed information about a particular container
lxc start [remote:][name]                 #start a stopped container
lxc stop [remote:][name]                  #stop a container
lxc restart [remote:][name]               #restart a container
lxc pause [remote:][name]                 #suspend a container so its processes do not consume CPU resources
lxc delete [remote:][name]                #remove a stopped container
lxc delete --force [remote:][name]        #remove a running container
lxc config edit [remote:][name]           #live edit a specific containers configuration
lxc profile list [remote:]                #list available container profiles
lxc profile show [remote:][profilename]   #list details of a particular profile
lxc profile edit [remote:][profilename]   #change the details in a profile
lxc config show [remote:][name]           #show container configuration
lxc exec [remote:][name] -- [command]     #run command in container
lxc exec [remote:][name] -- /bin/bash     #run bash in container
lxc file pull [remote:][container]/[path] [destination]      #download a copy from a container to a specific destination
lxc file push [filename] [remote][container]/[path]      #upload a file into a container
lxc file edit [remote:][container]/[path]                #edit file in container in local editor
lxc snapshot [remote:][container] [name]                 #create container snapshot
lxc snapshot restore [remote:][destination container] [snapshot name]     #restore container from snapshot
lxc copy [remote:][source container]/[snapshot name] [remote:][new container name]     #create new container from snapshot
lxc delete [remote:][container]/[snapshot]      #delete snapshot
lxc remote list     #list all remotes
```

### ssh:
```bash
ssh -X                                                 #connect with an untrusted X server
ssh-copy-id USER@REMOTE                                #copy public ssh id to remote
ssh -L PORT:127.0.0.1:PORT -C -N -l USER@REMOTE REMOTE #tunnel for VNC connection at localhost:PORT
```

### scp:
```text
scp username@from_host:file.txt /local/directory/		    #copy file from a remote host to local host:
scp file.txt username@to_host:/remote/directory/		    #copy file from local host to a remote host:
scp -r username@from_host:/remote/directory/  /local/directory/	    #copy directory from a remote host to local host:
scp -r /local/directory/ username@to_host:/remote/directory/	    #copy directory from local host to a remote host:
scp username@from_host:/remote/directory/file.txt username@to_host:/remote/directory/ #copy file from remote host to remote host:
```

### swap: _(chmod to 600)_
```text
swapoff -a                #disable swap on all swap devices
mkswap /SWAP/PATH         #set up a file as Linux swap area
swapon /SWAP/PATH         #enable swap on a file
swapon --show             #see if swap is active
/proc/sys/vm/swappiness   #swap niceness, 0-100. lower value corresponds with less use
sysctl vm.swappiness=##   #option to change above swappiness value
```

### systemd:
```text
systemctl                                   #list all running services
systemctl start foo.service                 #activate a service immediately
systemctl stop foo.service                  #deactivate a service immediately
systemctl restart foo.service               #restart a service
systemctl status foo.service                #show service status
systemctl enable foo.service                #enable service to start on boot
systemctl disable foo.service               #disable service to start on boot
systemctl is-enabled foo.service; echo $?   #check whether a service is already enabled or not
systemctl list-unit-files                   #list all services (enabled does not mean running!)
```

### user management:
```bash
adduser <username>                      #add user to system
adduser --home <directory> <username>   #adduser with specific home directory
adduser -g <groupname> <username>       #adduser with specific group membership
passwd <username>                       #change user password
usermod -h <directory> <username>       #change user home directory
addgroup <groupname>                    #add group to system
userdel -r <username>                   #delete user along with their home directory
groupdel <groupname>                    #delete a group
usermod -g <groupname> <username>       #change user's primary group
usermod -a -G <groupname> <username>    #adduser to an existing group as a secondary group
id <username>                           #see user's list of groups, GIDS, and UID
id -G -n <username>                     #see user's groups
cat /etc/passwd                         #list all users
cat /etc/group                          #list all groups 
```

### VirtualBox:
`VBoxManage` command:
```bash
list vms #list all virtual machines
list runningvms
modifyvm VM <option1> <arg1> ... <optionn> <argn>
```

### misc:
```text
du -h --max-depth=1 | sort -hr    #list directories and their size
watch -n 0.5 free -m              #every 0.5 seconds watch the memory usage
ctrl + alt + *                    #kill the front process
ctrl + alt + Fkey                 #switch to virtual console
alt + .                           #recall previous argument
grep installed /var/log/dpkg.log  #list packages with installation timestamps
dpkg-reconfigure tzdata           #reconfigure timezone data
```

### Reboot Even If System Utterly Broken:
```text
Alt+SysRq+R    #switch keyboard to 'raw' mode
Alt+SysRq+E    #send SIGTERM (termination) signal to all processes except mother init
Alt+SysRq+I    #send SIGKILL signal to all processes, a little more aggressive
Alt+SysRq+S    #sync all filesystems to prevent data loss
Alt+SysRq+U    #remount filesystems as read-only
Alt+SysRq+B    #forcefully reboot
```


### archives
```bash
7z a -p -mhe=on NAME.7z PATH  #create password protected 7z archive with encrypted metadata
7z a NAME.7z PATH             #create 7z archive
```

# Git Cheatsheet
### git
```bash
#history:
log --graph --full-history            #display visual log of complete working tree
log --oneline                         #collapse commits to one line each
log --simplify-by-decoration          #hist most linear commits, simplifying output
log -- FILE_A.EXT FILE_B.EXT          #show commit history for specific files
shortlog                              #simple version of git log showing authors and short message
show COMMIT_A...COMMIT_B              #output all commits in range from commit A to commit B
show --pretty="" --name-only COMMIT   #list all files touched in commit
remote set-url origin new.git.url/    #change remote url for repo
remote -v                             #view existing remote(s)

#basics:
master                                #default development branch
origin                                #default upstream repository
HEAD                                  #current branch
HEAD^                                 #parent of HEAD
HEAD~4                                #great-great grandparent of HEAD

#repo management:
git status		                        #file changes in working directory
git diff		                          #changes to tracked files
git log		                            #history of changes
git git blame $file	                  #who changed what and when in a file
git show $id	                        #commit identified by $ID
git branch		                        #all local branches
git reset --hard	                    #return to last committed state **cannot be undone!**

#make better branch the new master:
git checkout better_branch
git merge --strategy=ours --no-commit master
git commit                            # add information to the template merge message
git checkout master
git merge better_branch               # fast-forward master up to the merge
```


# Vim Cheatsheet
### vim
```text
#settings:
list/nolist     #whitespace
nu/nonu         #line numbers

#movement:
h        #move cursor left
j        #move cursor down
k        #move cursor up
l        #move cursor right
H        #move to top of screen
M        #move to middle of screen
L        #move to bottom of screen
w        #jump forwards to the start of a word
W        #jump forwards to the start of a word (words can contain punctuation)
e        #jump forwards to the end of a word
E        #jump forwards to the end of a word (words can contain punctuation)
b        #jump backwards to the start of a word
B        #jump backwards to the start of a word (words can contain punctuation)
0        #jump to the start of the line
^        #jump to the first non-blank character of the line
$        #jump to the end of the line
g_       #jump to the last non-blank character of the line
gg       #go to the first line of the document
G        #go to the last line of the document
5G       #go to line 5
fx       #jump to next occurrence of character x
tx       #jump to before next occurrence of character x
}        #jump to next paragraph (or function/block, when editing code)
{        #jump to previous paragraph (or function/block, when editing code)
zz       #center cursor on screen
Ctrl + b #move back one full screen
Ctrl + f #move forward one full screen
Ctrl + d #move forward 1/2 a screen
Ctrl + u #move back 1/2 a screen

#insert mode:
i        # insert before the cursor
I        # insert at the beginning of the line
a        # insert (append) after the cursor
A        # insert (append) at the end of the line
o        # append (open) a new line below the current line
O        # append (open) a new line above the current line
ea       # insert (append) at the end of the word

editing:
r        # replace a single character
J        # join line below to the current one
cc       # change (replace) entire line
cw       # change (replace) to the start of the next word
ce       # change (replace) to the end of the next word
cb       # change (replace) to the start of the previous word
c0       # change (replace) to the start of the line
c$       # change (replace) to the end of the line
s        # delete character and substitute text
S        # delete line and substitute text (same as cc)
xp       # transpose two letters (delete and paste)
.        # repeat last command
```
