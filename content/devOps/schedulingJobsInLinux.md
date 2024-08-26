---
title: "Scheduling Jobs In Linux"
date: 2020-08-29
slug: "schedulingJobsInLinux"
description: "Scheduling Jobs In Linux"
keywords: ["linux", "scheduling"]
draft: false
#tags: ["linux", "scheduling"]
math: false
toc: true
---


An absolutely essential task for any System Administrator is scheduling tasks to run at off-hours. Linux offers several scheduler options including `cron`, `anacron`, at and `systemd-timers`.

## `cron` – Standard Scheduling

`cron` is a system daemon that will run a task at a specified time (to the minute). This is exceptionally useful to run re-occurring jobs, such as system maintenance or backups. It has been the standard scheduling tool in Linux and UNIX for several decades.

Each user has their own separate `cron` schedule which can be accessed via `crontab -e` when logged in as that user. In server environments, individual users often do not have (m)any scheduled jobs, but the `root` user (or other power-user, such as the database user) may have hundreds.

The scheduler can be entered using the `crontab -e` command, and the format for entering a scheduled job is as follows:

``` bash
* * * * * commands
- - - - -
| | | | |
| | | | ------ Day of Week (0 - 7) (Sunday = 0 or 7)
| | | -------- Month (1 - 12)
| | ---------- Day of Month (1 - 31)
| ------------ Hour (0 - 23)
-------------- Minute (0 -59)
```

So the following entry in `crontab -e` would run every 30 minutes:

``` bash
0/30 * * * * /random/maintenance/script.sh
```

Additionally, ```crontab -e``` will accept these values:

``` bash
string         meaning
------         -------
@reboot        Run once, at startup.
@yearly        Run once a year, "0 0 1 1 *".
@annually      (same as @yearly)
@monthly       Run once a month, "0 0 1 * *".
@weekly        Run once a week, "0 0 * * 0".
@daily         Run once a day, "0 0 * * *".
@midnight      (same as @daily)
@hourly        Run once an hour, "0 * * * *".
```

-------------

![Crontab Guru](https://crontab.guru/) is a very useful interactive guide to scheduling `crontab -e` entries. Additionally, the official documentation is available at `man 5 crontab` and will fill in many more details.

There are a couple of “gotchas” with `cron`. Firstly, there is no `STDOUT` with `cron`. Scripts that produce standard output (writes to the terminal) will fail. Instead, redirect the output to a log file (either within `crontab -e` or within the script itself):

``` bash
# crontab entry with output redirected to a log file
0/30 * * * * /random/maintenance/script.sh >> /home/brad/cronLogs/maintenanceScript.log
```

The second thing to remember within `crontab -e` is not to use relative paths. Absolute paths are strongly recommended (even within the shell scripts running). However, this is an easy task to accomplish within any shell script by setting a `$BASEDIR` variable:

``` bash
#!/bin/bash
                    
# Declare the base directory
$BASEDIR='/myMainteanceScripts/'
                   
# Run some scripts
$BASEDIR/backupDirs.sh
$BASEDIR/deleteOldBackups.sh
$BASEDIR/cleanTempFiles.sh
```

## `anacron` – Loosely Scheduled

`anacron` is an adaptation of `cron`, but allows for tasks to be loosely scheduled based on system activity. When a script is placed within the `/etc/cron.daily` directory, it will run at some point during the 24 hour cycle, whenever system load is low enough to accommodate it. Other scheduling options include the `/etc/cron.hourly`, `/etc/cron.weekly` or `/etc/cron.monthly` directories.

`anacron` is great for servers, but it is even more useful for personal devices such as laptops, as scheduled tasks will be scheduled to run whenever the device is available. This means that if a device is powered off, `anacron` will simply perform the job later (within the scheduled parameters). This is different than `cron`, where if the time is missed for any reason, the task won’t run.

A fairly standard listing of tasks scheduled via `anacron` in Ubuntu 20.04 includes:

``` bash
cron.hourly:
total 0

cron.monthly:
total 4
-rwxr-xr-x 1 root root 313 May 29  2017 0anacron

cron.weekly:
total 16
-rwxr-xr-x 1 root root 312 May 29  2017 0anacron
-rwxr-xr-x 1 root root 211 Nov 12  2018 update-notifier-common
-rwxr-xr-x 1 root root 813 Feb 10  2019 man-db
-rwxr-xr-x 1 root root 730 Mar  8 12:15 apt-xapian-index

cron.daily:
total 52
-rwxr-xr-x 1 root root  384 Dec 12  2012 cracklib-runtime
-rwxr-xr-x 1 root root  311 May 29  2017 0anacron
-rwxr-xr-x 1 root root  376 Nov 20  2017 apport
-rwxr-xr-x 1 root root  355 Dec 29  2017 bsdmainutils
-rwxr-xr-x 1 root root 1478 Apr 20  2018 apt-compat
-rwxr-xr-x 1 root root  214 Nov 12  2018 update-notifier-common
-rwxr-xr-x 1 root root  377 Jan 21  2019 logrotate
-rwxr-xr-x 1 root root 1123 Feb 10  2019 man-db
-rwxr-xr-x 1 root root 1187 Jul 16  2019 dpkg
-rwxr-xr-x 1 root root  543 Jul 16  2019 mlocate
-rwxr-xr-x 1 root root  539 Apr 13 14:19 apache2
```

## `at` – One-Time Tasks

System Administrators (and power-users) often need to schedule one-time tasks for later (but don’t want the job to re-occur on a schedule). Linux has a very useful tool for this: `at`.

Quick note -> `at` isn’t installed by default within some Debian distributions. It can be installed with `apt install at`. It is installed by default on RHEL-based distributions.

Before using `at`, ensure the `atd` service is running with `systemctl status atd`. If the service isn’t active, `systemctl enable --now atd` will get the service running in the background. Using at is simple using its basic interactive shell:

``` bash
[brad@fedora32 Documents]$ at 4pm
   warning: commands will be executed using /bin/sh
   at> /home/brad/myBackupScript.sh
   at> /home/brad/cleanupDownloads.sh
   at> 
   job 3 at Sat Aug 29 16:00:00 2020
```

These commands have set up 2 jobs to run at 4 pm, a backup script and a cleanup script. Note that the output is caused by `Ctrl-D`, which exits the interactive shell. To view scheduled jobs, run `atq`:

``` bash
[brad@fedora32 Documents]$ atq
3       Sat Aug 29 16:00:00 2020 a brad
```

Finally, to remove a scheduled job, `atrm` will take care of it:

``` bash
[brad@fedora32 Documents]$ atrm 3
[brad@fedora32 Documents]$ atq
[brad@fedora32 Documents]$
```

# `systemd-timers` – An Updated Way to Schedule Jobs in Linux

`systemd timers` is an updated way to schedule jobs in systems that are running `systemd` (most of the mainstream distributions now run `systemd`). Many new packages (or redeveloped packages) are beginning to utilize it. The documentation includes `man 7 systemd-timer` and `man 7 systemd-time`. The timer files can be found at `/usr/lib/systemd/system`. Listing the timers that are on a system is simple:

``` bash
[root@fedora32 ~]# systemctl list-unit-files --type=timer
UNIT FILE                       STATE     VENDOR PRESET
chrony-dnssrv@.timer            disabled  disabled
dnf-makecache.timer             enabled   enabled
fstrim.timer                    enabled   enabled
fwupd-refresh.timer             disabled  disabled
logrotate.timer                 enabled   enabled
mdadm-last-resort@.timer        static    disabled
mlocate-updatedb.timer          enabled   enabled
raid-check.timer                disabled  disabled
systemd-tmpfiles-clean.timer    static    disabled
unbound-anchor.timer            enabled   enabled

10 unit files listed.
```

In order to schedule a job, both a `.timer` file and a `.service` file are required. The `.timer` file contains information about when the job will run, and the `.service` file contains information about what will actually run. The advantage is that there are significantly more options and control allocated using this system. For example, in the `.timer` file below, the OnBootSec property determines the job will run 10 minutes after bootup, but then the OnUnitInactiveSec property determines that the job will also run every hour:

``` bash
[root@fedora32 ~]# systemctl cat dnf-makecache.timer
   # /usr/lib/systemd/system/dnf-makecache.timer
   [Unit]
   Description=dnf makecache --timer
   ConditionKernelCommandLine=!rd.live.image
   # See comment in dnf-makecache.service
   ConditionPathExists=!/run/ostree-booted
   Wants=network-online.target
	
   [Timer]
   OnBootSec=10min # Runs 10 minutes after boot
   OnUnitInactiveSec=1h #  Runs every hour thereafter
   Unit=dnf-makecache.service
	
   [Install]
   WantedBy=timers.target
```

In order to activate the timer, simply use `systemctl enable .timer` and `systemctl start .timer` and it will start running on its intended schedule.

To determine when a job last ran, issue `systemctl status dnf-makecache.service`:

``` bash
[root@fedora32 ~]# systemctl status dnf-makecache.service
● dnf-makecache.service - dnf makecache
Loaded: loaded (/usr/lib/systemd/system/dnf-makecache.service; static; vendor preset: disabled)
Active: inactive (dead) since Sat 2020-08-29 14:38:30 ADT; 55min ago
TriggeredBy: ● dnf-makecache.timer
Main PID: 2963 (code=exited, status=0/SUCCESS)
CPU: 12.803s

Aug 29 14:38:22 fedora32.personalLaptop dnf[2963]: RPM Fusion for Fedora 32 - Free - Updates kB/s | 9.9 kB     00:00
Aug 29 14:38:23 fedora32.personalLaptop dnf[2963]: RPM Fusion for Fedora 32 - Free - Updates kB/s | 618 kB     00:00
Aug 29 14:38:24 fedora32.personalLaptop dnf[2963]: RPM Fusion for Fedora 32 - Free kB/s | 3.1 kB     00:00
Aug 29 14:38:25 fedora32.personalLaptop dnf[2963]: RPM Fusion for Fedora 32 - Nonfree - Updates kB/s | 9.7 kB     00:00
Aug 29 14:38:25 fedora32.personalLaptop dnf[2963]: RPM Fusion for Fedora 32 - Nonfree - Updates kB/s | 139 kB     00:00
Aug 29 14:38:26 fedora32.personalLaptop dnf[2963]: RPM Fusion for Fedora 32 - Nonfree    kB/s | 3.1 kB     00:00
Aug 29 14:38:30 fedora32.personalLaptop dnf[2963]: Metadata cache created.
Aug 29 14:38:30 fedora32.personalLaptop systemd[1]: dnf-makecache.service: Succeeded.
Aug 29 14:38:30 fedora32.personalLaptop systemd[1]: Finished dnf makecache.
Aug 29 14:38:30 fedora32.personalLaptop systemd[1]: dnf-makecache.service: Consumed 12.803s CPU

```

Notice that the service ran 55 minutes ago, meaning it will run again in 5 minutes.

Finally, a quick look at the service file itself shows what is actually executed every hour with this systemd timer:

``` bash
[root@fedora32 ~]# systemctl cat dnf-makecache.service
# /usr/lib/systemd/system/dnf-makecache.service
[Unit]
Description=dnf makecache
# On systems managed by either rpm-ostree/ostree, dnf is read-only;
# while someone might theoretically want the cache updated, in practice
# anyone who wants that could override this via a file in /etc.
ConditionPathExists=!/run/ostree-booted

After=network-online.target

[Service]
Type=oneshot
Nice=19
IOSchedulingClass=2
IOSchedulingPriority=7
Environment="ABRT_IGNORE_PYTHON=1"
ExecStart=/usr/bin/dnf makecache --timer
```

Notice that much more control is available, for example, the Nice=19 parameter means the job will be given very low priority by the system scheduler, so it will get done but not at the expense of higher priority jobs.

-------

While `cron` is still the most widely used scheduling tool in Linux, `systemd timers` are becoming more and more popular, particularly among package developers. All System Administrators and power-users should become familiar with `cron`, `anacron`, `at` and `systemd-timers`.
