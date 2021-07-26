# ansible_homelab

This is an attempt to learn ansible and set a home lab using an older PC. My desktop will act as my Ansible control host.

Before starting need to setup an SSH key for ansible and add it to the server.

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

