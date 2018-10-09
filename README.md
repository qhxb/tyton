# Tyton

[![license](https://img.shields.io/badge/license-GPL-brightgreen.svg)](https://github.com/nbulischeck/tyton/blob/master/LICENSE)
[![version](https://img.shields.io/badge/linux-4.4.0.31+-blue.svg?style=flat)](https://github.com/nbulischeck/tyton)

<img align="left" src="https://i.imgur.com/enDjxat.jpg" style="padding: 25px">

Linux Kernel-Mode Rootkit Hunter for 4.4.0-31+.

## Detected Attacks

**Syscall Table Hooking**
  
**Netfilter Hooking**

**Hidden Modules**

**Zeroed Process Inodes**

**Process Fops Hooking**

**Interrupt Descriptor Table Hooking**

&nbsp;

&nbsp;

&nbsp;

## Detection Methodology

**Syscall Table Hooking**: Search through the syscall table to see if any functions point outside the core kernel text section. If they do not point within the core kernel text section, it is likely that they have been hooked but to make sure we search all loaded modules to verify this.

**Netfilter Hooking**: Search through all possible Netfilter hook combinations and report all modules that have active Netfilter hooks. Netfilter hooks have a legitimate use, but it is unlikely that they would be present beyond those made for iptables, ebtables, and friends. They are uncommonly used in LKM rootkits to create backdoors by intercepting packets used to authenticate for port knocking.

**Hidden Modules**: `Sysfs` contains a multitude of ksets which in turn contain multiple kernel objects (kobjects). The kset `module_kset` within `sysfs` holds kobject references to all loaded kernel modules. By traversing this list, we can resolve each kobject back to its containing object (its referenced kernel module). When compared with the current module list entries from `find_module(kobj->mod->name)`, we can uncover modules who unlinked themselves from the list.

**Zeroed Process Inodes**: Search through `/proc` for all `linux_dirent` structures and examine the inodes to find any set to 0. An inode of 0 is typically ignored from directory listings which makes it a good candidate for rootkits to set directories to in order to hide their files.

**Process Fops Hooking**: Open the `/proc` file pointer and check to see if any file operations (namely `iterate`) point outside the core kernel text section.

**Interrupt Descriptor Table Hooking**: Search through the interrupt descriptor table to see if any entries point outside the core kernel text section. If they do not point within the core kernel text section, it is likely that they have been hooked but to make sure we search all loaded modules to verify this.

## Additional Features

**Notifications**: Users (including myself) do not actively monitor their journald logs, so a userland notification daemon has been included to monitor journald logs and display them to the user using libnotify.
