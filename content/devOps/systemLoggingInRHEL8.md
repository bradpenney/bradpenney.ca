---
title: "System Logging In RHEL8"
date: 2020-10-14
slug: "systemLoggingInRHEL8"
description: "System Logging In RHEL 8"
keywords: ["RHEL8", "logging"]
draft: false
#tags: ["RHEL8", "storage"]
math: false
toc: true
---

System logging is one of the most important topics for a System Administrator to understand. This quick video discusses some of the tools and techniques used to log system information in Red Hat 8 such as `rsyslog`, `journald`, `journalctl`, and `logrotate`.

{{< youtube VKxM0MmCw-o >}}

## `rsyslog`

Since 2007, every version of Red Hat has used the `rsyslogd` service to record system activity. A simple `systemctl status rsyslog` shows that the service is active and running by default. The file that controls `rsyslog` is `/etc/rsyslog.conf`. Each rule has a facility that the log is created for, the severity of message to be logged, and a destination of where the log should be stored. Any extra snap-in files should be located in `/etc/rsyslog.d/`. By default, this directory exists but is empty.

The default log location for `rsyslog` is `/var/log`. A quick listing shows that there is extensive default logging, with each log being large and relatively esoteric. Reading logs in this way is usually done via `grep` as reading the actual logs is cumbersome and very time consuming.

## `journald`

The `systemd-journald` service is available and enabled by default on Red Hat 7 and up. This service makes logs much more accessible and messages easier to find. It is the mechanism behind the `systemctl status` command, which is one of the most common ways to check the status of any service. The system journal is written to `/run/log/journal/<UUID>`. The configuration file is found at `/etc/systemd/journald.conf`. By default, `journald` is automatically cleared on system reboot, but simply changing the storage parameter in `journald .conf` to “persistent” will preserve journals across reboots.

## `journalctl`

Built into the `journald` service is the `journalctl` command, which offers a very powerful filtering system so the user can find specific information quickly and easily. For example, it is possible to search using `journalctl` to find messages only about a specific service, such as “NetworkManager”. Combining multiple filters is even more powerful, so using `journalctl -p err -u NetworkManager.service` shows only errors that have been encountered with Network Manager, making it much easier to filter through logs and find the relevant information. `journalctl` is a very powerful command, and well worth learning about in detail. `man journalctl` is a great place to start.

## `logrotate`

Logging information is essential for troubleshooting, but keeping logs forever may not be ideal, particularly if disk space is a consideration. The `logrotate` utility is automatically enabled in Red Hat 8, which discards logs after 5 weeks by default. `logrotate` is controlled by `/etc/logotate.conf`. Options in this file include rotating logs weekly, how many weeks to keep backlogs, whether to compress the files and more. Notice it also designates a location for custom `logrotate` schedules for RPM packages, by default this is found in the `/etc/logrotate.d/` directory. `logrotate` is run daily via `anacron`. This can be easily seen by visiting `/etc/cron.daily` and viewing the `logrotate` file. As with any task assigned to `cron.daily` this will be performed at some point throughout the day when system resources are not too busy.
