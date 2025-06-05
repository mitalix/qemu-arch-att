### qemu-arch-att

https://wiki.archlinux.org/title/Installation_guide


```bash
image_dir=../images/qemu-archlinux
image=${image_dir}/archlinux-2025.06.01-x86_64.iso
disk=${image_dir}/arch.img
```


Create a disk drive, that is a file image:
```bash
$ qemu-img create ${disk} 10G
```

Proceed to the install
```bash
$ qemu-system-x86_64  -m 2G -cdrom ${image} -hda ${disk}
```

Not all options are needed especially when creating a test virtual machine, but one of them is a nice convenience

```bash
# setfont ter-132b
```

Partition the disk drive that shows up as /dev/sda. See the section below for [Disk partitioning](#my-header).


Mount the partition:

```bash
mount /dev/sda1 /mnt
```
If you are behind a proxy, allow pacman to update the cache

```
# export http_proxy=cso.proxy.att.com:8888

```
Get to the installation
```
# pacstrap -K /mnt base linux linux-firmware
```
Ran into problems and one suggestion on the screen was to run
```bash
# pacman-key --init
```
After about 4 failing key checks, the troubleshooting section suggests

```bash
pacman-key --populate archlinux
```
which allowed the installation to continue

---
#### Additional information
Or proceed to the install, but try to add the user network
```bash
qemu-system-x86_64 -m 2G -cdrom ${image} -hda ${disk} -m 2G -net nic -net user,hostfwd=tcp::2222-:22
```


Then you can log into the system from the host:
```bash
# host > ssh -p 2222 italix@localhost
```


You could also use sftp when needed:
```bash
sftp localhost -oPort=2222
```


### <a id="my-header"></a> Disk partitioning

```bash
# fdisk /dev/sda
```
Start by "o -   create a new empty MBR (DOS) partition table"


```
Command (m for help): o
Created a new DOS (MBR) disklabel with disk identifier 0xd065093e.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-20971519, default 2048):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-20971519, default 20971519): +8G

Created a new partition 1 of type 'Linux' and of size 8 GiB.

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p):

Using default response p.
Partition number (2-4, default 2):
First sector (16779264-20971519, default 16779264):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (16779264-20971519, default 20971519):

Created a new partition 2 of type 'Linux' and of size 2 GiB.

Command (m for help): t
Partition number (1,2, default 2): 2
Hex code or alias (type L to list all): L

00 Empty            27 Hidden NTFS Win  82 Linux swap / So  c1 DRDOS/sec (FAT-
01 FAT12            39 Plan 9           83 Linux            c4 DRDOS/sec (FAT-
02 XENIX root       3c PartitionMagic   84 OS/2 hidden or   c6 DRDOS/sec (FAT-
03 XENIX usr        40 Venix 80286      85 Linux extended   c7 Syrinx
04 FAT16 <32M       41 PPC PReP Boot    86 NTFS volume set  da Non-FS data
05 Extended         42 SFS              87 NTFS volume set  db CP/M / CTOS / .
06 FAT16            4d QNX4.x           88 Linux plaintext  de Dell Utility
07 HPFS/NTFS/exFAT  4e QNX4.x 2nd part  8e Linux LVM        df BootIt
08 AIX              4f QNX4.x 3rd part  93 Amoeba           e1 DOS access
09 AIX bootable     50 OnTrack DM       94 Amoeba BBT       e3 DOS R/O
0a OS/2 Boot Manag  51 OnTrack DM6 Aux  9f BSD/OS           e4 SpeedStor
0b W95 FAT32        52 CP/M             a0 IBM Thinkpad hi  ea Linux extended
0c W95 FAT32 (LBA)  53 OnTrack DM6 Aux  a5 FreeBSD          eb BeOS fs
0e W95 FAT16 (LBA)  54 OnTrackDM6       a6 OpenBSD          ee GPT
0f W95 Ext'd (LBA)  55 EZ-Drive         a7 NeXTSTEP         ef EFI (FAT-12/16/
10 OPUS             56 Golden Bow       a8 Darwin UFS       f0 Linux/PA-RISC b
11 Hidden FAT12     5c Priam Edisk      a9 NetBSD           f1 SpeedStor
12 Compaq diagnost  61 SpeedStor        ab Darwin boot      f4 SpeedStor
14 Hidden FAT16 <3  63 GNU HURD or Sys  af HFS / HFS+       f2 DOS secondary
16 Hidden FAT16     64 Novell Netware   b7 BSDI fs          f8 EBBR protective
17 Hidden HPFS/NTF  65 Novell Netware   b8 BSDI swap        fb VMware VMFS
18 AST SmartSleep   70 DiskSecure Mult  bb Boot Wizard hid  fc VMware VMKCORE
1b Hidden W95 FAT3  75 PC/IX            bc Acronis FAT32 L  fd Linux raid auto
1c Hidden W95 FAT3  80 Old Minix        be Solaris boot     fe LANstep
1e Hidden W95 FAT1  81 Minix / old Lin  bf Solaris          ff BBT
24 NEC DOS

Aliases:
   linux          - 83
   swap           - 82
   extended       - 05
   uefi           - EF
   raid           - FD
   lvm            - 8E
   linuxex        - 85
Hex code or alias (type L to list all): 82

Changed type of partition 'Linux' to 'Linux swap / Solaris'.

Command (m for help): p
Disk arch.img: 10 GiB, 10737418240 bytes, 20971520 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd065093e

Device     Boot    Start      End  Sectors Size Id Type
arch.img1           2048 16779263 16777216   8G 83 Linux
arch.img2       16779264 20971519  4192256   2G 82 Linux swap / Solaris

Command (m for help): w
The partition table has been altered.


mi6577@CZBRN0475 UCRT64 ~
$ lsblk arch.img
-bash: lsblk: command not found

mi6577@CZBRN0475 UCRT64 ~
$ fdisk -l arch.img
Disk arch.img: 10 GiB, 10737418240 bytes, 20971520 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd065093e

Device     Boot    Start      End  Sectors Size Id Type
arch.img1           2048 16779263 16777216   8G 83 Linux
arch.img2       16779264 20971519  4192256   2G 82 Linux swap / Solaris

mi6577@CZBRN0475 UCRT64 ~
$

```

The final steps to create the file system and swap on those "disk" drives
```
# mkfs.ext4 /dev/sda1
```

If you created a partition for swap, initialize it with mkswap(8):
```
# mkswap /dev/sda2
```
