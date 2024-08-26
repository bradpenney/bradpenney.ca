---
title: "Managing Storage in RHEL8"
date: 2020-11-02
slug: "managingStorageInRHEL8"
description: "Managing Storage in RHEL8"
keywords: ["RHEL8", "storage"]
draft: false
#tags: ["RHEL8", "storage"]
math: false
toc: true
---

Managing storage is crucial to managing servers. This video covers basic storage techniques in RHEL8, including creating partitions using `parted`, making a file system, runtime mounting, and permanent mounting.

{{< youtube khPcZ6bVjjU >}}

## Disks vs Partitions

Let’s start with a quick review of what a disk is and what a partition is. The disk is the physical device that is connected to the motherboard inside the computer (or via an external port). Partitions are how the disk is divided – one disk can have many partitions but each partition is on a specific disk, at least with basic storage systems.

So a server will have one or many physical storage devices connected to it. To list all physical devices, run the `lsblk` command. This demo is being performed on a virtual machine, so the disks are `vda`, `vdb`, and `vdc`. This represents 3 hard disks attached to the virtual machine, simulating 3 hard disks installed in one server. On a non-virtual computer, you may encounter `sda`, `hda` (legacy), or even `nveme0n1`, which is a newer type of SSD.

## Master Boot Record vs GUID Partition Table

The GUID Partition Table is a much more modern and useful system. Master Boot Record, MBR, is a legacy system that should only be used in special cases, but could still be present in older legacy systems. In GPT, up to 128 partitions are available. The default tool to manage partitions in RHEL8 is `parted`. Issue the `parted` command followed by whatever physical device name you’d like to manage, so in this case, it would be `parted /dev/vdb`.

## Creating a Partition with `parted`

Inside `parted`, the help option lists the available commands and is very helpful. `print` shows the current partition table, in this case there is none.

So the first step is to designate a label, either MBR or GPT. We’ll use GPT, so the command is `mklabel gpt`. By the way, if you wanted MBR, the label would be `mklabel msdos`, which is counter-intuitive… After you’ve created the label, the easiest way to create a partition using parted is to simply type `mkpart` and it will prompt you for the required information. Enter the details as requested, and be sure to print the details before leaving parted to ensure the intended result has been achieved. Then quit `parted`.

Once you’ve created the partition using `parted`, issue `udevadm settle` to ensure that the kernel has picked up the new storage device.

## Creating a File System on the Partition

Discussing the various file systems available in RHEL8 is really its own video, suffice to say that XFS is the default file system and we’ll stick with that for this demonstration. To ensure you’re using the right partition, list them using `lsblk`. As mentioned, there are many file systems available, simply typing `mkfs.+TAB+TAB` shows a list. In our case, we’ll use the default XFS system, so issue `mkfs.xfs /dev/vdb1`.

## Runtime Mounting

So now we have a partition with a valid file system ready to be used by the system. The next step is to create a place that the file system will be mounted – where files saved on the partition will be located within the file system. In our case, we’ll create a new file system called `/data`, using the `mkdir /data` command.

Now let’s mount this file system in runtime to make sure it’s actually working correctly. The command will be mount `/dev/vdb1 /data`. To double-check this, run mount by itself to find the file system (usually the last entry).

## Permanent Mounting

As mentioned, this file system is now temporarily mounted – if the system were to reboot, it would no longer be available. To make it permanent, we need to edit ```/etc/fstab```, where all the file systems are listed. Let’s do that now.

Use `vi` to open `/etc/fstab`, and then add a line at the end. First the device name, the location in the file system where it can be found, the file system type, and then `defaults 0 0` will work for this system.

After a quick reboot, listing the mounted file systems using the mount command clearly shows that our partition is permanently mounted and available for usage.
