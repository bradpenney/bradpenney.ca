---
title: "Resetting Root Password in Linux"
date: 2020-09-27
slug: "resettingRootPasswordLinux"
description: "Resetting Root Password in Linux"
keywords: ["root password reset"]
draft: false
#tags: ["root password reset"]
math: false
toc: true
---

**This is for demonstration and educational purposes only. Enjoy responsibly!**

Misplacing the `root` password on a Linux system can be catastrophic. Whether intentional (malicious) or accidental, being unable to log into the `root` account can bring a system to a standstill – updates, user management, storage management, and a host of other services could be disrupted. Luckily, Linux offers a way to reset the root password without knowing the current password. The only caveat – physical access to the computer is required (and a little know-how!).

This demo will be performed on a CentOS 8 Virtual Machine, but could just as easily be performed on any physical computer (with Linux installed). The process is basically the same for all Linux computers that use Grub2, with the exception of SELinux usage (see below). The first step is to reboot the computer and pause at the Grub2 menu (usually a 5 second prompt during boot process to press any key):

![Resetting Root Password in Linux](/devops/changingRootPassword_01.png)

The system may have more than one kernel installed, simply select the correct (usually latest) one and press `e` in the Grub2 menu to edit the kernel arguments. This will result in a screen similar to this one:

![Resetting Root Password in Linux](/devops/changingRootPassword_02.png)

In this screen, find the line that starts with `linux` and modify the end of it. The `rhgb` argument enables “Red Hat Graphical Boot”, while the `quiet` argument causes the kernel to not display boot-up messages. Remove `rhgb quiet` and add `rd.break`, as seen below. The `rd.break` argument causes the boot process to display a root shell once InitRAM file system is loaded, before `systemd` has been initialized.

![Resetting Root Password in Linux](/devops/changingRootPassword_03.png)

Once the kernel arguments have been modified, press `Ctrl-X` to continue. After booting, a `root` shell will appear, without having to enter a password:

![Resetting Root Password in Linux](/devops/changingRootPassword_04.png)

Although a `root` shell has appeared, we’ve not gained access to the proper system yet. This prompt points to the InitRAM file-system, which resides only in RAM during the boot process, not the proper file-system that the system operates in normally. The normal file-system is actually mounted in `/sysroot` directory within the InitRAM file system. To make matters worse, `/sysroot` is read-only by default (at this stage). In order to reset the `root` password, it is necessary to mount `/sysroot` with read/write access. In the screenshot below, the last line of the result of the mount command shows current read-only access. The command to remount the `/sysroot` file-system in read/write mode is `mount -o remount,rw /sysroot`:

![Resetting Root Password in Linux](/devops/changingRootPassword_05.png)

Now that `/sysroot` is mounted in read/write mode, the next step is to navigate to the `/sysroot` directory and change it to the base directory:

![Resetting Root Password in Linux](/devops/changingRootPassword_06.png)

Notice that once the `/sysroot` directory becomes the `root` directory, the shell prompt changes. Once this has been accomplished, it is possible to change the	`root` password in the standard method using the `passwd` command:

![Resetting Root Password in Linux](/devops/changingRootPassword_07.png)

The previously unknown `root` password has been changed! However, as this is a CentOS 8 system with SELinux enabled, we need to create a file to signal SELinux to recreate its permissions. Failing to do this will result in an unbootable system, because various `systemd` services will not start. To avoid this, create a file called `/.autorelabel`:

![Resetting Root Password in Linux](/devops/changingRootPassword_08.png)

At this point, the procedure is complete. To summarize, we remounted the main file system in read/write, changed the `root` password, and prepared SELinux for this change. Press `Ctrl+D` twice, and the system will boot normally, with the new root password. This process will take longer than normal, as SELinux needs to recreate its polices (messages from SELinux during boot):

![Resetting Root Password in Linux](/devops/changingRootPassword_09.png)

Mission Complete! The `root` password has been reset on the system without knowing the previous `root` password!
