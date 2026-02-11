# Embedded_linux

1. Create a 1 GiB Virtual Disk Image

Command Used

```bash
dd if=/dev/zero of=sd.img bs=1M count=1024
```

Explanation

* `dd` → Low-level tool used to copy data
* `if=/dev/zero` → Input file that provides zero-filled data
* `of=sd.img` → Output disk image file
* `bs=1M` → Block size is 1 MB
* `count=1024` → Number of blocks (1024 MB = 1 GiB)

Expected Output

A file named `sd.img` with size **1.1 GiB**.

---

2. Difference Between DOS/MBR and GPT Partition Schemes

| Feature        | DOS / MBR   | GPT                   |
| -------------- | ----------- | --------------------- |
| Max disk size  | 2 TB        | Very large (ZB range) |
| Max partitions | 4 primary   | Up to 128             |
| Reliability    | Low         | High (backup tables)  |
| Boot support   | Legacy BIOS | UEFI                  |

---

3. File Systems and Their Usage

FAT16

* Simple and lightweight
* Used in boot partitions
* Limited size support
* No Linux permissions
  

FAT32

* Supports larger partitions than FAT16
* Common in USB drives and SD cards
* No Linux permissions

EXT4

* Linux native file system
* Supports large files and journaling
* Used for root file systems

---

4. Partitioning the Virtual Disk Image

Partitions Layout

1. **First Primary Partition**

   * Size: 200 MB
   * Bootable
   * File system: FAT16

2. **Second Primary Partition**

   * Size: Remaining space
   * File system: EXT4

Tool Used

```bash
fdisk sdcard.img
```

Expected Output

Two primary partitions created successfully.

---

5. Loop Devices

What Are Loop Devices?

Loop devices allow Linux to treat a **file as a block device**, like a real disk.

Why Linux Uses Them

* Disk image testing
* Embedded systems development
* Mounting ISO or image files

Commands

a. Create a Loop Device

```bash
sudo losetup -fP sdcard.img
```

b. List Loop Devices

```bash
losetup -a
```

c. Detach a Loop Device

```bash
sudo losetup -d /dev/loopX
```

---

6. Check Current Loop Device Limit

```bash
cat /sys/module/loop/parameters/max_loop
```



7. Expanding the Number of Loop Devices

Yes, it is possible.

Temporary

```bash
sudo modprobe loop max_loop=64
```

---

8. Attach the Virtual Disk Image as a Loop Device

```bash
sudo losetup -fP sdcard.img
```

Expected Output

* `/dev/loopX`
* `/dev/loopXp1`
* `/dev/loopXp2`

---

9. Format the Virtual Disk Image Partitions

Boot Partition (FAT16)

```bash
sudo mkfs.vfat -F 16 -n boot /dev/loopXp1
```

**Explanation**

* `-F 16` → FAT16 file system
* `-n boot` → Volume label

Root File System (EXT4)

```bash
sudo mkfs.ext4 -L rootfs /dev/loopXp2
```

---

10. mount and unmount Commands

mount

Used to attach a file system to a directory.

```bash
mount /dev/device /mount_point
```

unmount

Used to safely detach a mounted file system.

```bash
umount /mount_point
```

---

11. Block Device vs Character Device

| Block Device            | Character Device               |
| ----------------------- | ------------------------------ |
| Data accessed in blocks | Data accessed as stream        |
| Supports buffering      | No buffering                   |
| Example: HDD, SD card   | Example: keyboard, serial port |

---

12. Create Mount Points and Mount Partitions

Create Mount Points

```bash
mkdir boot rootfs
```

Mount Partitions

```bash
sudo mount /dev/loopXp1 boot
sudo mount /dev/loopXp2 rootfs
```


