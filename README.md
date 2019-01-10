# Raspbian Toolbox

Raspbian Toolbox is a collection of tools for Raspbian image customization.

## Set up a developing environment for Raspbian image customization

### The selection of Linux distro

Debian or Ubuntu is preferred for Raspbian image customization works. But other Linux distro can be used. I do not see any obstruction here.

### Set up Qemu User Emulation

Qemu User Emulation [1] is used to run arm executable files. Install the `qemu-user-static` and `binfmt-support` packages.

```
# apt-get install qemu qemu-user-static binfmt-support
```

[1] https://wiki.debian.org/RaspberryPi/qemu-user-static

### Get the lastest Raspbian image

Get the lastest Respbian from the official web site of Raspberry Pi [2]. The lite image is prefered for Raspbian image customization works.

[2] https://www.raspberrypi.org/downloads/raspbian/

## Tools

### `raspbian-lvm-reform`

Reform Raspbian image to LVM partition layout.

```
# raspbian-lvm-reform /path/to/raspbian.img
```

This will generate a new image file `/path/to/raspbian-lvm.img`, which will be in the same directory, along with original image file. The default LVM partition layout look like the following.

```
Filesystem             Size  Used Avail Use% Mounted on
/dev/mapper/sd0-root   488M  207M  246M  46% /
/dev/sda1              197M   46M  152M  24% /boot
/dev/mapper/sd0-home   488M  796K  452M   1% /home
/dev/mapper/sd0-opt    488M   41M  412M  10% /opt
/dev/mapper/sd0-srv    120M  1.6M  110M   2% /srv
/dev/mapper/sd0-tmp    488M  780K  452M   1% /tmp
/dev/mapper/sd0-var    976M  162M  748M  18% /var
/dev/mapper/sd0-usr    2.0G  559M  1.3G  31% /usr
/dev/mapper/sd0-local  120M  1.6M  110M   2% /usr/local
```

The default partition layout can be customized by edit the part of configurations in the script. Just change the number of the size, add lines for additional file systems.

```bash
################################
####     Configurations     ####
################################
TARGET_IMAGE_SIZE_LIMIT="6GB"
TARGET_BOOT_PART_OFFSET="4MiB"
TARGET_BOOT_PART_SIZE="200MiB"
TARGET_LVM_VG_NAME="sd0"
TARGET_LVM_LV_000=("root"  "/"          "512MiB" "ext4")
TARGET_LVM_LV_001=("home"  "/home"      "512MiB" "ext4")
TARGET_LVM_LV_002=("opt"   "/opt"       "512MiB" "ext4")
TARGET_LVM_LV_003=("srv"   "/srv"       "128MiB" "ext4")
TARGET_LVM_LV_004=("tmp"   "/tmp"       "512MiB" "ext4")
TARGET_LVM_LV_005=("var"   "/var"      "1024MiB" "ext4")
TARGET_LVM_LV_006=("usr"   "/usr"      "2048MiB" "ext4")
TARGET_LVM_LV_007=("local" "/usr/local" "128MiB" "ext4")
################################
```

### `raspbian-chroot`

Run command or interactive shell inside the Raspbian image.

#### Get an interactive shell

You can do whatever change you want inside the Raspbian image.

Example

```
# raspbian-chroot /path/to/raspbian.img
root@raspbian:~# id
uid=0(root) gid=0(root) groups=0(root)
root@raspbian:~# pwd
/root
root@raspbian:~# mount
/dev/loop1 on / type ext4 (rw,noatime,data=ordered)
/dev/loop0 on /boot type vfat (rw,relatime,fmask=0077,dmask=0077,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro)
udev on /dev type devtmpfs (rw,nosuid,relatime,size=2000092k,nr_inodes=500023,mode=755)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
root@raspbian:~# df
Filesystem     1K-blocks   Used Available Use% Mounted on
/dev/loop1       1708880 970988    633036  61% /
/dev/loop0         43539  22191     21348  51% /boot
udev             2000092      0   2000092   0% /dev
root@raspbian:~# ls -l /
total 79
drwxr-xr-x   2 root root  4096 Jun 27 00:21 bin
drwx------   3 root root  2560 Jan  1  1970 boot
drwxr-xr-x   2 root root  4096 Jun 27 00:13 debootstrap
drwxr-xr-x  17 root root  3340 Sep 21 10:43 dev
drwxr-xr-x  84 root root  4096 Jun 27 01:09 etc
drwxr-xr-x   3 root root  4096 Jun 27 00:17 home
drwxr-xr-x  16 root root  4096 Jun 27 00:30 lib
drwx------   2 root root 16384 Jun 27 01:08 lost+found
drwxr-xr-x   2 root root  4096 Jun 27 00:03 media
drwxr-xr-x   2 root root  4096 Jun 27 00:03 mnt
drwxr-xr-x   3 root root  4096 Jun 27 00:17 opt
dr-xr-xr-x 140 root root     0 Sep 21 04:58 proc
drwx------   2 root root  4096 Sep  6 09:05 root
drwxr-xr-x   5 root root  4096 Jun 27 00:18 run
drwxr-xr-x   2 root root  4096 Jun 27 00:30 sbin
drwxr-xr-x   2 root root  4096 Jun 27 00:03 srv
dr-xr-xr-x  13 root root     0 Sep 21 04:58 sys
drwxrwxrwt   2 root root  4096 Jun 27 01:09 tmp
drwxr-xr-x  10 root root  4096 Jun 27 00:03 usr
drwxr-xr-x  11 root root  4096 Jun 27 00:03 var
root@raspbian:~# exit
```

#### Run command directly inside the Raspbian image

Example

```
# raspbian-chroot /path/to/raspbian.img file /bin/bash
/bin/bash: ELF 32-bit LSB executable, ARM, EABI5 version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-armhf.so.3, for GNU/Linux 3.2.0, BuildID[sha1]=b1e8e19f3b7b388468f2914bfb2ae8ac6b9f0478, stripped
```

#### Copy files into the Raspbian image

Use of `tar` is preferred for file copy.

Example

```
# ( cd /path/to/src && tar cf - ) | raspbian-chroot /path/to/raspbian.img 'cd /path/to/dst && tar xf -'
```
