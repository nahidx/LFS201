[Previous Chapter](../Ch38-grub/notes_Ch38.md) - [Table of Contents](../README.md#table-of-contents) - [Next Chapter](../Ch40-backuprecovery/notes_Ch40.md)

---

# Chapter 39 init: SystemV, Upstart, systemd - Notes

## 39.3 Learning Objectives:
- Understand the importance of the **init** process
- Understand how **systemd** (and **Upstart**) arose and how they work.
- Use **systemctl** to configure and control **systemd**.
- Explain how the traditional **SysVinit** method works and how it incorporates **runlevels** and what happens in each one.
- Use **chkconfig** and **service** (and alternative utilities) to start and stop services or make them persistent across reboots when using **SysVinit**.

## 39.4 The init Process
`/sbin/init` (usually called **init**): first user-level process (or task) run on system, continues to run until system is shutdown. Traditionally, has been considered **parent** of all user processes, although technically that is not true, as some processes are started directly by kernel.

init coordinates later stages of boot process, configures all aspects of the environment, and start processes needed for logging into the system. **init** also works closely with kernel in cleaning up after processes when they terminate.

Traditionally, nearly all distributions based **init** process on UNIX's venerable **SysVinit**. However, this scheme was developed decades ago under rather different circumstances:
- Target was multi-user mainframe systems (and not personal computers, laptops, and other devices)
- Target was single processor system
- Startup (and shutdown) time was not an important matter; it was far less important than getting things right.

Startup viewed as serial process, divided into series of sequential stages. Each stage required completion before the next could proceed. Thus, startup did not easily take advantage of parallel processing that could be done on multiple processors or cores.

Secondly, shutdown/reboot was seen as a relatively rare event, and exactly how long it took was not considered important.

Modern systems have required newer methods with enhanced capabilities.

## 39.5 Startup Alternatives
To deal with intrinsic limitations in SysVinit, new methods of controlling system startup developed. While there are others, two main schemes adopted by Enterprise distributors:
1. **Upstart**: developed by Ubuntu, first included in 6.10 release in 2006, made default in 9.10 release in 2009. Also adopted in Fedora 9 (in 2008), in RHEL 6 and its clones (CentOS, Scienctific Linux, Oracle Linux), and in openSUSE was offered as an option since version 11.3. Has also been used in various embedded and mobile devices.
2. **systemd**: more recent, Fedora was fist major distribution to adopt in 2011. Standard since RHEL 7 and Ubuntu 16.04.

RHEL 7 based on systemd and every other major Linux distribution has adopted it, made it the default or announced plans to do so. Main systemd developers closely tied to Linux kernel community. Even Ubuntu phased out Upstart in its favor.

Migrations to newer schemes complicated, bugs and missing features can be very disabling, so there have been essential required compatibility layers. Thus, SysVinit utilities and methods will persist for long time, even if under the hood things are quite different.

History of all this and controversies quite complicated. Colorful personalities have ensured not all discussion is of technical nature In our context, will not look through this lens.

Next, going to concentrate on systemd and SysVinit with briefer section on Upstart. (even though RHEL 6 and some other distributions had used Upstart, has been thoroughly hidden behind compatibility layer using normal SysVinit utilities.)

## 39.6 systemd
systemd system and session manager for Linux rapidly taking root in all major distributions. Features include following:
- Is compatible with SysVinit scripts
- Boots faster than previous systems
- Provides aggressive parallelization capabilities
- Uses socket and D-Bus activation for starting services
- Replaces shell scripts with programs
- Offers on-demand starting of daemons
- Keeps track of processes using cgroups
- Supports creating snapshots and restoring of the system state
- Maintains mount and automount points
- Implements an elaborate transactional dependency-based service control logic
- Can work as a drop-in replacement for SysVinit.

Instead of bash scripts, systemd uses `.service` files. In addition, systemd sorts all daemons into their own Linux cgroups (control groups).

systemd backward compatible with SysVinit and the concept of runlevels supported via runlevel **targets**. **telinit** program emulated to work with runlevels.

## 39.7 systemd Configuration Files
Although systemd prefers to use set of new standardized configuration files, can also use distribution-dependent legacy configuration files as fall-back.

Example of new configuration file: `/etc/hostname`, which would replace `/etc/sysconfig/network` in Red Hat, `/etc/hostname` in SUSE, and `/etc/hostname` (adopted as the standard) in Debian.

Other files might include:
- `/etcvconsole.conf`: default keyboard mapping and console font
- `/etc/sysctl.d/*.conf`: drop-in directory for kernel **sysctl** parameters
- `/etc/os-release` distribution ID file

systemd backward compatible with SysVinit, so using old commands will generally work. Supports the concept of runlevels, supported via runlevel *targets*, and **telimit** is emulated to work with runlevels.

## 39.8 systemctl
**systemctl**: main utility for managing services. Basic syntax:
```shell
$ systemctl [options] command [name]
```
Below, can see some examples of how you can use **systemctl**:
- To show the status of everything that systemd controls:
  ```shell
  $ systemctl
  ```
- To show all available services:
  ```shell
  $ systemctl list-units -t service --all
  ```
- To show only active services:
  ```shell
  $ systemctl list-units -t service
  ```
- To start (activate) one or more units:
  ```shell
  $ sudo systemctl start foo
  $ sudo systemctl start foo.service
  $ sudo systemctl start /path/to/foo.service
  ```
  where a unit can be a service or a socket.
- To stop (deactivate) a service:
  ```shell
  $ sudo systemctl stop foo.service
  ```
- To enable/disable a service:
  ```shell
  $ sudo systemctl enable sshd.service
  $ sudo systemctl disable sshd.service
  ```
  Equivalent of `chkconfig on/off` and doesn't actually start the service.

**Note**: Some **systemctl** commands in the above examples can be run as non-root user, others require running as root or with **sudo**.

For an excellent summary fo how to go from **SysVinit** to **systemd**, see the [SysVinit to Systemd Cheatsheet](https://fedoraproject.org/wiki/SysVinit_to_Systemd_Cheatsheet).

## 39.10 A Word About SysVinit
SysVinit was standard method for starting and shutting down systems, and for managing services for many years.

However, has been replaced on all major Linux distributions by systemd. Still value in covering how it works, as it is being replaced, as compatibility layers have been put into place to ensure the older methods can still be used. Furthermore, sometimes third party software has not been updated to use **systemctl** methods of systemd (a prominent example: VMWare, as of this writing).

Besides discussion of runlevels and other ingredients, **chkconfig** and **service** commands will also be explained. Eventually, will probably move to drop discussion of SysVinit, as well as Upstart.



##

[Back to top](#)

---

[Previous Chapter](../Ch38-grub/notes_Ch38.md) - [Table of Contents](../README.md#table-of-contents) - [Next Chapter](../Ch40-backuprecovery/notes_Ch40.md)