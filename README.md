# Linux Cheat Sheet

This is my personal Linux cheat sheet, for when I don't feel like reading man pages.
Use it at your own discretion!


## Computer Information:

List hardware specifics
- `nproc` Number of processors
- `lscpu` CPU info
- `lshw` Overall hardware info, run as root user
- `lspci` PCI bus
- `lsusb` USB controllers
- `lsblk` Block devices


## Distribution Information:

Distribution release file(s)  
`cat /etc/*-release`

On a GNU systemd based system, use the `hostnamectl` command


## User Management

Add a user in the `sudo` group with a home directory
`useradd name --comment "Full Name" --groups sudo --create-home --user-group`

## dd (data define)

Data define, or convert and copy a file  
`dd bs=## if=PATH/INFILE of=PATH/OUTFILE status=progress`

Write an image to disk  
`dd bs=4M if=PATH/FILE.iso of=/dev/DISK status=progress oflag=sync`

Back up a disk as an image file  
`dd if=/dev/DISK conv=sync,noerror bs=64K | gzip -c  > /PATH/FILE.img.gz`


## fdisk (fixed disks)

List disk info  
`fdisk -l`

Manipulate disk partition table  
`fdisk /PATH/TO/DISK`

Use a curses based textual user interface  
`cfdisk`


## SMART Disk Monitoring

Check if disk is SMART capable
`smartctl --info /dev/dsk`

Read error log
`smartctl --log=error /dev/dsk`

Test hard disk
`smartctl --test=short /dev/dsk`
`smartctl --test=long /dev/dsk`


## Miscellaneous Disk Management

List and modify partitions  
`parted`

Output block device attributes  
`blkid`

Make and format a file system  
`mkfs.<format> /PATH/TO/PARTITION`


## Kernel:

Get kernel name, version and machine hardware  
`uname -mrs`

Output info directly from file containing kernel version  
`cat /proc/version`


## LUKS (Linux Unified Key Setup)

Encrypt a partition verbosely and verify the passphrase  
`cryptsetup -y -v luksformat /dev/part`

List info for LUKS partition, including key slots  
`cryptsetup luksDump /dev/part`

Modify or add a passphrase
- `cryptsetup luksAddKey /dev/part`
- `cryptsetup luksChangeKey /dev/part -s i`
- `cryptsetup luksRemoveKey  /dev/part`

Remove the ith passphrase  
`cryptsetup luksKillSlot /dev/part i`

Open or close LUKS partition with "target" as name
- `cryptsetup luksOpen /dev/part <target>`
- `cryptsetup luksClose <target>`


## ssh

Establish an SSH tunnel for VNC connection  
`ssh -L PORT:127.0.0.1:PORT -C -N -l USER@REMOTE REMOTE`


## scp

Copy a file from remote to local  
`scp username@remote:file.txt /local/directory/`

Copy a file from local to remote  
`scp file.txt username@remote:/remote/directory/`


## swap _(chmod to 600)_

Disable swap on all swap devices  
`swapoff -a`

Set a file as Linux swap area  
`mkswap /SWAP/PATH`

Enable swap on a file  
`swapon /SWAP/PATH`

Check if swap is active  
`swapon --show`

Check swap niceness between 0-100, Lower values correspond with less use  
`/proc/sys/vm/swappiness`

Assign swap niceness, as described above  
`sysctl vm.swappiness=100`


## Syncthing

Set up Syncthing as a system service  
`systemctl enable syncthing@user.service`

Read Syncthing logs  
`journalctl -e -u syncthing@user.service`

Use the web GUI at [localhost:8384](http://localhost:8384)


## systemd

List all running services:
`systemctl`

Configure services in real time
- `systemctl start foo.service`
- `systemctl stop foo.service`
- `systemctl restart foo.service`
- `systemctl status foo.service`

Configure services at boot time
- `systemctl enable foo.service`
- `systemctl disable foo.service`

List all services  
`systemctl list-unit-files`


## VirtualBox

List all virtual machines  
`VBoxManage list vms`

List running virtual machines  
`VboxManage list runningvms`

Modify a specific machine  
`VboxManage modifyvm VM <option1> <arg1> ... <optionn> <argn>`


## Miscellaneous

Display file or file system status  
`stat <option> <file>`

List directories and their size  
`du -h --max-depth=1 | sort -hr`

Kill the front process  
`ctrl + alt + *`

Switch to virtual console  
`ctrl + alt + Fkey`

Recall previous argument  
`alt + .`

List packages with installation timestamps  
`grep installed /var/log/dpkg.log`

Reconfigure timezone data  
`dpkg-reconfigure tzdata`


## Reboot Even If System Utterly Broken

- `Alt+SysRq+R` Switch keyboard to raw input mode
- `Alt+SysRq+E` Send SIGTERM signal to all processes
- `Alt+SysRq+I` Send SIGKILL signal to all processes
- `Alt+SysRq+S` Sync all file systems to prevent data loss
- `Alt+SysRq+U` Remount file systems as read-only
- `Alt+SysRq+B` Forcefully reboot


## Archiving Files

Create an encrypted 7zip file with encrypted metadata  
`7z a -p -mhe=on FILE.7z PATH`

Decrypt the same 7zip file  
`7z x FILE.7z`

Create an encrypted tarball with GPG  
`tar -cvzf - FILE | gpg -c > FILE.tar.gz.gpg`

Decrypt an encrypted tarball with GPG  
`gpg -d FILE.tar.gz.gpg | tar -xvzf -`


## Battery and Power

Get power sources
`upower -e`

Get power source information
`upower -i <source>`

Battery metrics, including thermals
`acpi --verbose`

## Wireguard

### Generate key pairs
```
umask 077  # This makes sure credentials don't leak in a race condition
wg genkey | tee privatekey | wg pubkey > publickey
```

### Configuration files in `/etc/wireguard/wgX.conf`
Server:
```
[Interface]
Address = 10.10.10.1
PrivateKey = <server's privatekey>
ListenPort = 51820

[Peer]
PublicKey = <client's publickey>
AllowedIPs = 10.10.10.2/32
```

Client:
```
[Interface]
Address = 10.10.10.2
PrivateKey = <client's privatekey>
ListenPort = 21841

[Peer]
PublicKey = <server's publickey>
Endpoint = <server's ip>:51820
AllowedIPs = 10.10.10.0/24,<server LAN subnet>

# This is for if you're behind a NAT and
# want the connection to be kept alive.
PersistentKeepalive = 25
```

### Quick up/down over interface:
```
wg-quick up wg0
wg-quick down wg0
```

### Forward server LAN over iptables:
Add the following at the end of the `[Interface]` section in the server config
file.

```
PostUp   = iptables -A FORWARD -i %i -j ACCEPT; iptables -A FORWARD -o %i -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -D FORWARD -o %i -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
```

### Run as a service on client
```
sudo chown -R root:root /etc/wireguard/
sudo chmod -R og-rwx /etc/wireguard/*
sudo systemctl enable wg-quick@wg0.service
sudo systemctl start wg-quick@wg0.service
```
