---
toc: true
toc_sticky: true
layout: single
title: "Linux System Administration: Configuration, Diagnostics && Management"
author: "Danni"
tags:
  - Linux
  - OS
---


The first time I got in touch with the Linux world was trying to dual boot Ubuntu on my ThinkPad. Partition, boot-repair and grub issues got me struggling with multiple erros I hadn't solved independetly in my entire life. At that time I even made a note to myself that I would never like this "mess". 

Interestingly enough, I got into this fasinating world through another door shrotly after -- Infra. The Infrastructure team I'm interning at needs to support a company of over 1000 people so the struggle was REAL on my first day.(Why I choose Infra you ask? Maybe in another post :P) But after almost over a year of ansorbing knowledge from great engineers, I have gained so much insights and have come a long way no that I looked back to me a year ago.

I'm going to record tips I get in Linux Administration and will constantly make updates.

### Basic Configuration

##### Hostname

```shell
$ hostname
$ hostname -i  # display the hostname IP address
$ hostname -I  # display the network address and all configured network interfaces
$ hostname -d  # the name of the DNS domain, e.g, com, ca, ...
$ hostname -f  # FQDN (Fully Qualified Domain Name)
$ hostname -A  # all the FQDNs of the machine.
$ hostname -a  # all the alias name (i.e., substitute names)
$ sudo hostname NEW_HOSTNAME  # change or set hostname
```

##### Time Zone

Consider using UTC(Greenwish Mean Time) if not sure whihc time zone to set.

1. In Ubuntu or Debian:

```shell
dpkg-reconfigure tzdata
```

2. In CentOS or Arch Linux:

```shell
timedatectl list-timezones  # view available time zones

timedatectl set-timezone 'America/New_York'
```

3. Or set manually:

Find the appropriate zone file in `/usr/share/zoneinfo/` and link that file to `/etc/localtime`.

```shell
ln -sf /usr/share/zoneinfo/UTC /etc/localtime # Universal Coordinated Time

ln -sf /usr/share/zoneinfo/EST /etc/localtime # Eastern Standard Time

ln -sf /usr/share/zoneinfo/US/Central /etc/localtime # American Central Time (including Daylight Savings Time)

ln -sf /usr/share/zoneinfo/US/Eastern /etc/localtime # American Eastern Time (including Daylight Savings Time)
```


### Disgnostics

##### Monitor I/O Usage with vmsta

The `vmstat` tool provides information about memory, swap utilization, I/O wait, and system activity. It is particularly useful for diagnosing I/O-related issues.

```shell
vmstat 1 20  # check if thinking have I/O ussye
```

Above command runs every second, twenty times, giving a sample of the current state of the system. 

The output run on my Ubuntu 18.04 is:
```shell
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0      0 1286440 1822580 7126708    0    0    54   220  178  116 28 18 52  1  0
 0  0      0 1285968 1822580 7126616    0    0     0     0  360  937  1  0 99  0  0
 0  0      0 1288200 1822580 7126616    0    0     0     0  514 1492  4  2 93  0  0
 0  0      0 1288932 1822580 7126616    0    0     0    72  412  941  2  2 97  0  0
 0  0      0 1288572 1822580 7126616    0    0     0     0  369  954  2  0 98  0  0
 0  0      0 1288324 1822580 7126616    0    0     0     0  369  934  1  0 99  0  0
 0  0      0 1288076 1822588 7126608    0    0     0    12  336  869  1  0 99  1  0
 0  0      0 1287828 1822588 7126616    0    0     0     0  404  940  1  1 98  0  0
 0  0      0 1287828 1822592 7126612    0    0     0    24  365  905  1  0 98  1  0
 0  0      0 1286224 1822592 7127992    0    0     0     0  791 2037  4  2 94  0  0
 0  0      0 1281256 1822592 7129872    0    0     0     0 1803 5588 13  6 81  0  0
 0  0      0 1283984 1822592 7126892    0    0     0     0 1192 1807  2  1 97  0  0
 2  0      0 1280480 1822592 7129324    0    0     0     0 1339 3045  5  3 92  0  0
 0  0      0 1276380 1822592 7131288    0    0     0     8 1286 2401  4  1 94  0  0
 0  0      0 1275272 1822592 7130808    0    0     0     0  613 2678  6  2 92  0  0
 1  0      0 1273320 1822592 7132072    0    0     0     0  833 3117  7  2 91  0  0
 1  0      0 1275552 1822592 7130280    0    0     0     0 1189 4266 12  4 84  0  0
 0  0      0 1275612 1822592 7130112    0    0     0     0  809 3178  8  3 89  0  0
 0  0      0 1275176 1822592 7130120    0    0     0   440  979 3462  9  2 81  7  0
 0  0      0 1275112 1822592 7130124    0    0     0     0  766 2990  9  2 89  0  0

```

The memory and swap columns provide the same kind of information provided by the “free -m” command, albeit in a slightly harder to understand format. The most relevant information produced by this command is the wa column, which is the final column in most implementations. This field displays the amount of time the CPU spends waiting for I/O operations to complete.

If this number is consistently and considerably higher than 0, you might consider taking measures to address your IO usage. However, if the vmstat output resembles the above, you can be sure in the knowledge that you’re not experiencing an IO-related issues.

If you are experiencing an intermittent issue, you will need to run vmstat when you experience the issue in order to properly diagnose or rule out an I/O issue. vmstat output can sometimes help support diagnose problems.

##### Monitor Processes, Memory, and CPU Usage with htop

Though `htop` is installed by default in most of the systems, I higjly recommend install it as you can have a more organized, real-time view of the current state of the system. Here are some commands depending which distribution you use:

```shell

apt-get install htop

# or

sudo snap install htop # version 2.2.0

yum install htop
pacman -S htop
emerge sys-process/htop

# Then just run htop

htop
```

To quit, use `F10` or `Q` or `Ctrl` plus `C`.

Here's a sample screenshot:


![htop](/assets/images/post/htop.png)
<small class="img-hint">Output of `htop` command</small>

### File management

##### Symbolic Links

Symbolic linking, colloquially “symlinking”, allows you to create an object in your filesystem that points to another object on your filesystem. 

This is useful when you need to provide users and applications access to specific files and directories without reorganizing your folders. This way you can provide restricted users access to your web-accessible directories without moving your `DocumentRoot` into their home directories.

```shell
ln -s /home/username/config-git/etc-hosts /etc/hosts

ln -s [/path/to/target/file] [/path/to/location/of/sym/link]
```

This creates a link of the file etc-hosts at the location of the system’s /etc/hosts file. More generically.

##### Package Management

1. Check packages

```shell
dpkg -l

dpkg -l | less  # return the same list as the plain “dpkg -l
```

![dpkg](/assets/images/post/dpkg.png)
<small class="img-hint">Output of `dpkg` command</small>

And to view a specific list of packages, use `grep`. For example, if I want to view Python:

```shell
dpkg -l | grep "python"
```

2. Find Package information

This will search the local package database for a given term and generate a list with descriptions -- the full records for all of the packages and not simply the titles and the descriptions displayed here, hence the inclusion of vim-nox and groovy which both mention python in their descriptions. 

```shell
apt-cache search [package-name]

# To see the full record on a specific package:

apt-cache show [package-name]
```

##### Text, 'grep'

1. Search for string in files

PLEASE, `grep`. I would also personally note that learning <u>regular expression</u> can help a lot. 

```shell
grep "^Subject:.*HELP.*" /home/username/mbox

grep -i "alice" ~/org/*.txt  # wild card
```

### Reference

[command list](https://www.linode.com/docs/tools-reference/linux-system-administration-basics/)
