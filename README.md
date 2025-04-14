# MK808-Linux

Out-of-box Ubuntu for MK808 Android TV Stick (RK3066 Chip)

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
    - It's possible to fit rootfs in ROM system partition, SD cards is more flexible

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

### iomem

```plaintext
linux@ubuntu:~> sudo cat /proc/iomem   
00000000-00000000 : mali fb
  00000000-00000000 : mali sdram
    00000000-00000000 : fb2 buf
1010e000-1010ffff : lcdc1 reg
  1010e000-1010ffff : rk30-lcdc
10110000-10113fff : rk29-ipp
10114000-10115fff : rga
  10114000-10115fff : rga_io
10116000-10117fff : rk30-hdmi
  10116000-10117fff : rk30-hdmi
10118000-10119fff : rk29_i2s.0
  10118000-10119fff : rk29_i2s
1011c000-1011dfff : rk29_i2s.2
  1011c000-1011dfff : rk29_i2s
10124000-10125fff : rk_serial.0
  10124000-10125fff : rk_serial
10180000-101bffff : usb20_otg
101c0000-101fffff : usb20_host
10214000-10217fff : rk29_sdmmc.0
10218000-1021bfff : rk29_sdmmc.1
10500000-10503fff : rk29xxnand
20018000-2001bfff : rk29-pl330.1
  20018000-2001bfff : rk29-pl330
2002d000-2002dfff : rk30_i2c.0
  2002d000-2002dfff : rk30_i2c
2002f000-2002ffff : rk30_i2c.1
  2002f000-2002ffff : rk30_i2c
20056000-20057fff : rk30_i2c.2
  20056000-20057fff : rk30_i2c
2005a000-2005bfff : rk30_i2c.3
  2005a000-2005bfff : rk30_i2c
2005e000-2005ffff : rk30_i2c.4
  2005e000-2005ffff : rk30_i2c
20060000-20063fff : rk30-tsadc
  20060000-20063fff : rk30-tsadc
20068000-2006bfff : rk_serial.3
  20068000-2006bfff : rk_serial
2006c000-2006ffff : rk30-adc
  2006c000-2006ffff : rk30-adc
20078000-2007bfff : rk29-pl330.2
  20078000-2007bfff : rk29-pl330
60000000-937fffff : System RAM
  605a4000-60b5dadb : Kernel text
  60b5e000-6144ba27 : Kernel data
93800000-957fffff : ipp buf
  93800000-957fffff : rk-fb
95800000-967fffff : fb0 buf
  95800000-967fffff : rk-fb
96800000-9fffffff : System RAM
```

### clocks

```plaintext
 sudo cat /proc/clocks   
pd_dbg      on  0 Hz usecount = 0
pd_gpu      on  0 Hz usecount = 0
pd_video    on  0 Hz usecount = 0
pd_display  on  0 Hz usecount = 1
    pd_ipp      0 Hz usecount = 0 parent = pd_display
    pd_rga      0 Hz usecount = 0 parent = pd_display
    pd_cif1     0 Hz usecount = 0 parent = pd_display
    pd_cif0     0 Hz usecount = 0 parent = pd_display
    pd_lcdc1    0 Hz usecount = 1 parent = pd_display
    pd_lcdc0    0 Hz usecount = 0 parent = pd_display
pd_peri     on  0 Hz usecount = 0
pclkin_cif1 off 0 Hz usecount = 0
    cif1_in     0 Hz usecount = 0 parent = pclkin_cif1
    inv_cif1    0 Hz usecount = 0 parent = pclkin_cif1
pclkin_cif0 off 0 Hz usecount = 0
    cif0_in     0 Hz usecount = 0 parent = pclkin_cif0
    inv_cif0    0 Hz usecount = 0 parent = pclkin_cif0
hsadc_ext   0 Hz usecount = 0
rmii_clkin  0 Hz usecount = 0
xin27m      27 MHz usecount = 0
xin24m      24 MHz usecount = 8
    timer2      off 24 MHz usecount = 0 parent = xin24m
    timer1      on  24 MHz usecount = 1 parent = xin24m
    timer0      on  24 MHz usecount = 1 parent = xin24m
    uart3       24 MHz usecount = 0 parent = xin24m
    uart2       24 MHz usecount = 1 parent = xin24m
    uart1       24 MHz usecount = 0 parent = xin24m
    uart0       24 MHz usecount = 0 parent = xin24m
    otgphy1     on  24 MHz usecount = 1 parent = xin24m
    otgphy0     on  24 MHz usecount = 1 parent = xin24m
    tsadc       off 50 KHz usecount = 0 parent = xin24m
    saradc      on  1 MHz usecount = 1 parent = xin24m
    general_pll 297 MHz usecount = 4 parent = xin24m
        gpu         off 148.500000 MHz usecount = 0 parent = general_pll
        aclk_vdpu   off 297 MHz usecount = 0 parent = general_pll
            hclk_vdpu   off 74.250000 MHz usecount = 0 parent = aclk_vdpu
        aclk_vepu   off 297 MHz usecount = 0 parent = general_pll
            hclk_vepu   off 74.250000 MHz usecount = 0 parent = aclk_vepu
        cif_out_pll 297 MHz usecount = 0 parent = general_pll
            cif1_out_div off 29.700000 MHz usecount = 0 parent = cif_out_pll
                cif1_out    29.700000 MHz usecount = 0 parent = cif1_out_div
            cif0_out_div off 29.700000 MHz usecount = 0 parent = cif_out_pll
                cif0_out    29.700000 MHz usecount = 0 parent = cif0_out_div
        dclk_lcdc1_div 148.500000 MHz usecount = 1 parent = general_pll
            dclk_lcdc1  on  148.500000 MHz usecount = 2 parent = dclk_lcdc1_div
        aclk_lcdc1_rga_parent on  297 MHz usecount = 1 parent = general_pll
            aclk_rga    off 297 MHz usecount = 0 parent = aclk_lcdc1_rga_parent
            aclk_cif1   off 297 MHz usecount = 0 parent = aclk_lcdc1_rga_parent
            aclk_lcdc1  on  297 MHz usecount = 1 parent = aclk_lcdc1_rga_parent
        aclk_lcdc0_ipp_parent off 297 MHz usecount = 0 parent = general_pll
            aclk_ipp    off 297 MHz usecount = 0 parent = aclk_lcdc0_ipp_parent
            aclk_cif0   off 297 MHz usecount = 0 parent = aclk_lcdc0_ipp_parent
            aclk_lcdc0  off 297 MHz usecount = 0 parent = aclk_lcdc0_ipp_parent
        hsadc_pll_div on  29.700000 MHz usecount = 0 parent = general_pll
            hsadc       29.700000 MHz usecount = 0 parent = hsadc_pll_div
            hsadc_frac_div off 1.485000 MHz usecount = 0 parent = hsadc_pll_div
        uart_pll    297 MHz usecount = 0 parent = general_pll
            uart3_div   off 297 MHz usecount = 0 parent = uart_pll
                uart3_frac_div off 14.850000 MHz usecount = 0 parent = uart3_div
            uart2_div   off 297 MHz usecount = 0 parent = uart_pll
                uart2_frac_div off 14.850000 MHz usecount = 0 parent = uart2_div
            uart1_div   off 297 MHz usecount = 0 parent = uart_pll
                uart1_frac_div off 14.850000 MHz usecount = 0 parent = uart1_div
            uart0_div   off 297 MHz usecount = 0 parent = uart_pll
                uart0_frac_div off 14.850000 MHz usecount = 0 parent = uart0_div
        aclk_periph on  148.500000 MHz usecount = 7 parent = general_pll
            aclk_peri_axi_matrix on  148.500000 MHz usecount = 1 parent = aclk_periph
            aclk_cpu_peri on  148.500000 MHz usecount = 1 parent = aclk_periph
            aclk_peri_niu on  148.500000 MHz usecount = 1 parent = aclk_periph
            dma2        on  148.500000 MHz usecount = 1 parent = aclk_periph
            aclk_smc    off 148.500000 MHz usecount = 0 parent = aclk_periph
            hclk_periph on  148.500000 MHz usecount = 12 parent = aclk_periph
                hclk_pidfilter on  148.500000 MHz usecount = 1 parent = hclk_periph
                nandc       on  148.500000 MHz usecount = 1 parent = hclk_periph
                hclk_mac    on  148.500000 MHz usecount = 1 parent = hclk_periph
                hclk_emem_peri on  148.500000 MHz usecount = 1 parent = hclk_periph
                hclk_peri_ahb_arbi on  148.500000 MHz usecount = 1 parent = hclk_periph
                hclk_peri_axi_matrix on  148.500000 MHz usecount = 1 parent = hclk_periph
                hclk_hsadc  off 148.500000 MHz usecount = 0 parent = hclk_periph
                hclk_emmc   off 148.500000 MHz usecount = 0 parent = hclk_periph
                emmc        off 6.187500 MHz usecount = 0 parent = hclk_periph
                hclk_sdio   on  148.500000 MHz usecount = 1 parent = hclk_periph
                sdio        on  24.750000 MHz usecount = 1 parent = hclk_periph
                hclk_sdmmc  on  148.500000 MHz usecount = 1 parent = hclk_periph
                sdmmc       on  49.500000 MHz usecount = 1 parent = hclk_periph
                hclk_usb_peri on  148.500000 MHz usecount = 2 parent = hclk_periph
CLKSEL19           :bb8ea60
CLKSEL20           :bb8ea60
CLKSEL21           :b01
CLKSEL22           :900
CLKSEL23           :bb8ea60
CLKSEL24           :178b
CLKSEL25           :0
CLKSEL26           :0
CLKSEL27           :300
CLKSEL28           :101
CLKSEL29           :913
CLKSEL30           :0
CLKSEL31           :8080
CLKSEL32           :8080
CLKSEL33           :101
CLKSEL34           :1df
CLKGATE0          :6602
CLKGATE1          :ff04
CLKGATE2          :d6b0
CLKGATE3          :3f9b
CLKGATE4          :2000
CLKGATE5          :1104
CLKGATE6          :f73
CLKGATE7          :b226
CLKGATE8          :1fb
CLKGATE9          :0
GLB_SRST_FST:0
GLB_SRST_SND:0
CLKGATE0          :0
CLKGATE1          :0
CLKGATE2          :0
CLKGATE3          :0
CLKGATE4          :0
CLKGATE5          :0
CLKGATE6          :0
CLKGATE7          :0
CLKGATE8          :0
CRU MISC    :0
GLB_CNT_TH  :64
```