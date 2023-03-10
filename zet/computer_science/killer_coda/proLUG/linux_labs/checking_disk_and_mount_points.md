# Checking Disk and Mounting Points

Check physical disk information

Echo the number of physical disks you have into `/root/disks`

Echo the number of partitions of that disk into `/root/partitions`

* Check disk information and count partitions
  - `fdisk -l`

```bash
Disk /dev/loop0: 63.2 MiB, 66265088 bytes, 129424 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/loop1: 67.83 MiB, 71106560 bytes, 138880 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/loop2: 47.101 MiB, 50319360 bytes, 98280 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/vda: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: D75B0394-501D-41E7-94C7-29E9642F257A

Device      Start      End  Sectors  Size Type
/dev/vda1  227328 41943006 41715679 19.9G Linux filesystem
/dev/vda14   2048    10239     8192    4M BIOS boot
/dev/vda15  10240   227327   217088  106M EFI System

Partition table entries are not in disk order.
```

  - `fdisk -l | grep -i vd`

```bash
Disk /dev/vda: 20 GiB, 21474836480 bytes, 41943040 sectors
/dev/vda1  227328 41943006 41715679 19.9G Linux filesystem
/dev/vda14   2048    10239     8192    4M BIOS boot
/dev/vda15  10240   227327   217088  106M EFI System
```

Why did we use VD?

* Let's use another command to see that information another way.
  - `lsblk`

```bash
ME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0     7:0    0 63.2M  1 loop /snap/core20/1634
loop1     7:1    0 67.8M  1 loop /snap/lxd/22753
loop2     7:2    0   48M  1 loop /snap/snapd/17336
vda     252:0    0   20G  0 disk 
|-vda1  252:1    0 19.9G  0 part /
||-vda14 252:14   0    4M  0 part 
|`-vda15 252:15   0  106M  0 part /boot/efi
```

  - `blkid`

```bash
/dev/vda1: LABEL="cloudimg-rootfs"
UUID="666195bb-9c58-470d-9495-743ff99e48c8" TYPE="ext4"
PARTUUID="1b586e7b-ba4c-4d6b-9ca6-2502f02cf595"
/dev/vda15: LABEL_FATBOOT="UEFI" LABEL="UEFI" UUID="B8F2-0510"
TYPE="vfat" PARTUUID="27df778d-f6e2-4441-b310-124faa31cc3e"
/dev/loop0: TYPE="squashfs"
/dev/loop1: TYPE="squashfs"
/dev/loop2: TYPE="squashfs"
/dev/vda14: PARTUUID="aab173d6-e275-429d-bb29-e66fbfa1c06b"
```

The solution is:

* `echo 1 > /root/disks`
* `echo 3 > /root/partitions`

## Check file system types and mount points

Echo the file system type of the root partition into `/root/fstype`

Echo the name of the file that defines all the mount points into
`/root/mountinfo`

* Check what partition the root (/) filesystem is mounted from
  - `mount | grep vda`

```bash
/dev/vda1 on / type ext4 (rw,relatime)
/dev/vda15 on /boot/efi type vfat
(rw,relatime,fmask=0077,dmask=0077,codepage=437,iocharset=iso8859-1,shortname=mixed,errors=remount-ro)
```

Check the file system written to that partition.

* Let's use another command to see that information another way
  - `blkid /dev/vda1`

```bash

```

* So you see the type is ext4. Write that out to `/root/fstype`
  - `blkid /dev/vda1 > /root/fstype`

```bash

```

