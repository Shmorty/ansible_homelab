# ansible_homelab

This is an attempt to learn ansible and set a home lab using an older PC. My desktop will act as my Ansible control host.

Before starting need to setup an SSH key for ansible and add it to the server.

## create key pairs
	ssh-keygen -t ed25519 -C paul default  # with pass hase 
	ssh-keygen -t ed25519 -C ansible       # no pass phrase, specify diff location/name

## ssh to host
to add fingerprint to known_hosts

## add keys
	ssh-copy-id -i ~/.ssh/id_ed25519.pub 10.0.0.42
	ssh-copy-id -i ~/.ssh/ansible.pub 10.0.0.42

## configure ansible to use key
create file ansible.cfg
add contents
	[default]
	private_key_file = ~/.ssh/ansible

## Graphics setup
In order to get display manager working on this particular machine I need to install firmware-amd-graphics which in non-free and requires an update to /apt/etc/sources.list, append "contrib non-free" to end of deb 

	deb http://deb.debian.org/debian/ buster main contrib non-free

followed by:
	$ sudo apt update
	$ sudo apt install firmware-amd-graphics

this can all be done using ssh, after a reboot graphical interface should be available.

We also need to configure system to not sleep when idle, we'll try and do this using ansible.
edit /etc/gdm3/greeter.dconf-defaults 
add sleep-inactive-ac-type='blank'

## ad-hoc ansible commands
ansible all -m ping
ansible all -m apt -a update-cache=true --become --ask-become-pass
ansible all -m apt -a "upgrade=dist" --become --ask-become-pass

## file server setup
I've manually installed zfs and created a pool
	sudo apt install linux-headers-`uname -r`
	apt install zfsutils-linux

	$ sudo /sbin/modprobe zfs
	$ sudo zpool create tank raidz sdb sdc
	$ sudo mkdir -p /data
	$ sudo zfs create -o mountpoint=/data tank/data
	$ sudo zpool status tank

	$ lsblk
	NAME                 MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
	sda                    8:0    0  74.5G  0 disk 
	├─sda1                 8:1    0   487M  0 part /boot
	├─sda2                 8:2    0     1K  0 part 
	└─sda5                 8:5    0    74G  0 part 
	├─atlas--vg-root   254:0    0  13.9G  0 lvm  /
	└─atlas--vg-swap_1 254:1    0   976M  0 lvm  [SWAP]
	sdb                    8:16   0 931.5G  0 disk 
	├─sdb1                 8:17   0 931.5G  0 part 
	└─sdb9                 8:25   0     8M  0 part 
	sdc                    8:32   0 931.5G  0 disk 
	├─sdc1                 8:33   0 931.5G  0 part 
	└─sdc9                 8:41   0     8M  0 part 
	sr0                   11:0    1  1024M  0 rom 
