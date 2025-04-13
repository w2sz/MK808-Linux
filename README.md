# MK808-Linux

Ubuntu for MK808 Android TV Stick (RK3066 Chip)

## How To

0. Download images and extract
    - `kernel.tar.gz` -> `recovery-1080p.img` ... Linux kernel 3.0.8
    - `linuxroot.img.tar.xz` -> `linuxroot.img` ... root file system
1. Flash rootfs image to SD card
    - SD card Minimum 1GB, recommend >8GB
    - FS is **ext2**. Use whatever tool you like.
    - Then run `resize2fs`
    - Edit if required:
        - `/etc/network/interfaces`
        - `/etc/wpa_supplicant/wireless.conf`
        - `/etc/fstab`
        - `/etc/hostname` - default "ubuntu"
        - `/etc/hosts`
2. Compile flashing tool
    - source file under `tool/`
    - `gcc -o rkflashtool rkflashtool.c -lusb-1.0 -O2 -W -Wall -s`
3. Flash kernel to TV stick
    - Backup ROM `sudo rkflashtool r 0x00000000 0x01000000 > myflashbackup.bin`
    - Backup Kernel `sudo rkflashtool r 0x00004000 0x00004000 > mk808kernel.img`
    - Unplug stick power, re-plug it in while holding down reset button. LED will remain off in DFU mode
    - Flash `sudo rkflashtool w 0x00008000 0x00010000 < recovery-1080p.img`
    - Power cycle
3. Setup.
    - Install SD card & power cycle
    - If you see two penguins, kernel is up
    - If you see tty, login
        - user: `linux`
        - passwd: `linux`

## Weird stuff
    - ext4 in 2024 has incompatible features than ext4 in 2014 :(, so just use ext2 

## Reference

- Tutorial
  - https://medium.com/swlh/running-debian-linux-on-mk808-android-tv-c150ff1afe5d
  - https://github.com/sgjava/ubuntu-mini/
- Tool
  - https://github.com/justgr/arnova-tools
- Asset
  - https://github.com/olegk0/rk3x_kernel_3.0.36


## Appendix

### Kernel Boot Arguments
```plaintext
root=LABEL=linuxroot init=/sbin/init loglevel=2 rootfstype=ext4 rootwait mtdparts=rk29xxnand:0x00002000@0x00002000(misc),0x00004000@0x00004000(kernel),0x00008000@0x00010000(recovery),-@0x0051A000(system)
```

### Hardware Spec

```plaintext
linux@ubuntu:~> ./sysinfo 
=== System Overview ===
Linux ubuntu 3.0.8+ #26 SMP PREEMPT Mon Mar 18 11:30:34 MSK 2013 armv7l armv7l armv7l GNU/Linux

=== CPU Information ===
Processor       : ARMv7 Processor rev 0 (v7l)
processor       : 0
BogoMIPS        : 1631.46

processor       : 1
BogoMIPS        : 1631.46

Features        : swp half thumb fastmult vfp edsp neon vfpv3 
CPU implementer : 0x41
CPU architecture: 7
CPU variant     : 0x3
CPU part        : 0xc09
CPU revision    : 0

Hardware        : RK30board
Revision        : 0000
Serial          : 0000000000000000

=== Memory Information ===
             total       used       free     shared    buffers     cached
Mem:          948M       343M       605M       304K        12M       280M
-/+ buffers/cache:        50M       898M
Swap:           0B         0B         0B
MemTotal:         971208 kB
MemFree:          619780 kB
Buffers:           12992 kB
Cached:           287040 kB
SwapCached:            0 kB
Active:           145644 kB
Inactive:         165536 kB
Active(anon):      11288 kB
Inactive(anon):      160 kB
Active(file):     134356 kB
Inactive(file):   165376 kB
Unevictable:           0 kB
Mlocked:               0 kB
HighTotal:        155648 kB
HighFree:           3608 kB
LowTotal:         815560 kB
LowFree:          616172 kB
SwapTotal:             0 kB
SwapFree:              0 kB
Dirty:                28 kB
Writeback:             0 kB
AnonPages:         11116 kB
Mapped:             5136 kB
Shmem:               304 kB
Slab:              20808 kB
SReclaimable:      15544 kB
SUnreclaim:         5264 kB
KernelStack:         552 kB
PageTables:          384 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:      485604 kB
Committed_AS:      50204 kB
VmallocTotal:     122880 kB
VmallocUsed:       50028 kB
VmallocChunk:      40892 kB

=== Storage Information ===
Filesystem                    Size  Used Avail Use% Mounted on
udev                           10M  4.0K   10M   1% /dev
tmpfs                         190M  200K  190M   1% /run
/dev/disk/by-label/linuxroot  118G  682M  112G   1% /
none                          4.0K     0  4.0K   0% /sys/fs/cgroup
none                          5.0M     0  5.0M   0% /run/lock
none                          475M  100K  475M   1% /run/shm
none                          100M     0  100M   0% /run/user
/dev/mmcblk0p1 on / type ext4 (rw)
proc on /proc type proc (rw,noexec,nosuid,nodev)
sysfs on /sys type sysfs (rw,noexec,nosuid,nodev)
none on /sys/fs/cgroup type tmpfs (rw)
none on /sys/fs/fuse/connections type fusectl (rw)
none on /sys/kernel/debug type debugfs (rw)
udev on /dev type devtmpfs (rw,mode=0755)
devpts on /dev/pts type devpts (rw,noexec,nosuid,gid=5,mode=0620)
tmpfs on /run type tmpfs (rw,noexec,nosuid,size=10%,mode=0755)
none on /run/lock type tmpfs (rw,noexec,nosuid,nodev,size=5242880)
none on /run/shm type tmpfs (rw,nosuid,nodev)
none on /run/user type tmpfs (rw,noexec,nosuid,nodev,size=104857600,mode=0755)

=== Network Information ===
eth0      Link encap:Ethernet  HWaddr 00:22:f4:4f:39:b1  
          inet addr:192.168.12.74  Bcast:192.168.12.255  Mask:255.255.255.0
          inet6 addr: fd3b:2f5e:5b72:0:8d3b:b99f:9a8f:e57b/64 Scope:Global
          inet6 addr: 2607:fb90:df21:8aa:8d3b:b99f:9a8f:e57b/64 Scope:Global
          inet6 addr: fd3b:2f5e:5b72:0:222:f4ff:fe4f:39b1/64 Scope:Global
          inet6 addr: 2607:fb90:df21:8aa:222:f4ff:fe4f:39b1/64 Scope:Global
          inet6 addr: fe80::222:f4ff:fe4f:39b1/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:37115 errors:0 dropped:30 overruns:0 frame:0
          TX packets:11184 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
... (30 lines left)
Collapse
message.txt
5 KB
﻿
linux@ubuntu:~> ./sysinfo 
=== System Overview ===
Linux ubuntu 3.0.8+ #26 SMP PREEMPT Mon Mar 18 11:30:34 MSK 2013 armv7l armv7l armv7l GNU/Linux

=== CPU Information ===
Processor       : ARMv7 Processor rev 0 (v7l)
processor       : 0
BogoMIPS        : 1631.46

processor       : 1
BogoMIPS        : 1631.46

Features        : swp half thumb fastmult vfp edsp neon vfpv3 
CPU implementer : 0x41
CPU architecture: 7
CPU variant     : 0x3
CPU part        : 0xc09
CPU revision    : 0

Hardware        : RK30board
Revision        : 0000
Serial          : 0000000000000000

=== Memory Information ===
             total       used       free     shared    buffers     cached
Mem:          948M       343M       605M       304K        12M       280M
-/+ buffers/cache:        50M       898M
Swap:           0B         0B         0B
MemTotal:         971208 kB
MemFree:          619780 kB
Buffers:           12992 kB
Cached:           287040 kB
SwapCached:            0 kB
Active:           145644 kB
Inactive:         165536 kB
Active(anon):      11288 kB
Inactive(anon):      160 kB
Active(file):     134356 kB
Inactive(file):   165376 kB
Unevictable:           0 kB
Mlocked:               0 kB
HighTotal:        155648 kB
HighFree:           3608 kB
LowTotal:         815560 kB
LowFree:          616172 kB
SwapTotal:             0 kB
SwapFree:              0 kB
Dirty:                28 kB
Writeback:             0 kB
AnonPages:         11116 kB
Mapped:             5136 kB
Shmem:               304 kB
Slab:              20808 kB
SReclaimable:      15544 kB
SUnreclaim:         5264 kB
KernelStack:         552 kB
PageTables:          384 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:      485604 kB
Committed_AS:      50204 kB
VmallocTotal:     122880 kB
VmallocUsed:       50028 kB
VmallocChunk:      40892 kB

=== Storage Information ===
Filesystem                    Size  Used Avail Use% Mounted on
udev                           10M  4.0K   10M   1% /dev
tmpfs                         190M  200K  190M   1% /run
/dev/disk/by-label/linuxroot  118G  682M  112G   1% /
none                          4.0K     0  4.0K   0% /sys/fs/cgroup
none                          5.0M     0  5.0M   0% /run/lock
none                          475M  100K  475M   1% /run/shm
none                          100M     0  100M   0% /run/user
/dev/mmcblk0p1 on / type ext4 (rw)
proc on /proc type proc (rw,noexec,nosuid,nodev)
sysfs on /sys type sysfs (rw,noexec,nosuid,nodev)
none on /sys/fs/cgroup type tmpfs (rw)
none on /sys/fs/fuse/connections type fusectl (rw)
none on /sys/kernel/debug type debugfs (rw)
udev on /dev type devtmpfs (rw,mode=0755)
devpts on /dev/pts type devpts (rw,noexec,nosuid,gid=5,mode=0620)
tmpfs on /run type tmpfs (rw,noexec,nosuid,size=10%,mode=0755)
none on /run/lock type tmpfs (rw,noexec,nosuid,nodev,size=5242880)
none on /run/shm type tmpfs (rw,nosuid,nodev)
none on /run/user type tmpfs (rw,noexec,nosuid,nodev,size=104857600,mode=0755)

=== Network Information ===
eth0      Link encap:Ethernet  HWaddr 00:22:f4:4f:39:b1  
          inet addr:192.168.12.74  Bcast:192.168.12.255  Mask:255.255.255.0
          inet6 addr: fd3b:2f5e:5b72:0:8d3b:b99f:9a8f:e57b/64 Scope:Global
          inet6 addr: 2607:fb90:df21:8aa:8d3b:b99f:9a8f:e57b/64 Scope:Global
          inet6 addr: fd3b:2f5e:5b72:0:222:f4ff:fe4f:39b1/64 Scope:Global
          inet6 addr: 2607:fb90:df21:8aa:222:f4ff:fe4f:39b1/64 Scope:Global
          inet6 addr: fe80::222:f4ff:fe4f:39b1/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:37115 errors:0 dropped:30 overruns:0 frame:0
          TX packets:11184 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:46525805 (46.5 MB)  TX bytes:1897547 (1.8 MB)

ip6tnl0   Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  
          NOARP  MTU:1452  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

sit0      Link encap:IPv6-in-IPv4  
          NOARP  MTU:1480  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)


=== Kernel Modules ===
Module                  Size  Used by
sg                     20460  0 
bcm40181              341831  0 
```