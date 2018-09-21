# Raspbian Toolbox

Raspbian Toolbox is a collection of tools for Raspbian image customization.

## Set up a developing environment for Raspbian image customization

### The selection of Linux distro

Debian or Ubuntu is preferred for Raspbian image customization works. But other Linux distro can be used. Ido not see any obstruction here.

### Set up Qemu User Emulation

Qemu User Emulation [1] is used to run arm executable files. Install the `qemu-user-static` and `binfmt-support` packages.

```
apt-get install qemu qemu-user-static binfmt-support
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
