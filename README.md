# Arch-Guide

This is my personal installation guide based on 
the ArchWiki. It includes additional comments that
explain each step and its variations in more detail.
I've also added some recommendations for customizing the OS.

# Table of contents

# Pre-installation

## Keyboard layout

The keyboard layouts are stored in the keymaps directory which
is `/usr/share/kbd/keymaps/`. Then it is divided in 
architecture, then in layouts, and then languages. Available 
layouts can be listed with `localectl`. 

```bash
localectl list-keymaps
```

In order to change to layout use `loadkeys` and the name of the file.

```bash
loadkeys us
```

## Boot mode

To verify the boot mode is UEFI check the UEFI bitness, if 
there is no output, then the boot mode is BIOS.

```bash
cat /sys/firmware/efi/fw_platform_size
```

## Internet connection

First check that the NIC is enabled.

```bash
ip link
```

If it is not running, manually start it.

```bash
ip link set [interface] up
```

Ethernet is usually working, however WiFi needs to be set up.
Depending on the encryption protocol, different tools are needed.
First scan for available networks, then connect to the network, and
finally get a local IP assigned. Test the connection with `ping`.

```bash
iwlist [interface] scan

# Open/WEP
iwconfig [interface] essid [SSID] key s:[PASSWORD]

# WPA/WPA2
wpa_passphrase [SSID] [PASSWORD] > /etc/wifi
wpa_supplicant -B -i [interface] -c /etc/wifi

dhclient

ping 8.8.8.8
```

## Update clock

To ensure correct installation, an updated system clock is needed 
therefore it must be synchronized using `timedatectl`.

```bash
timedatectl status
timedatectl set-ntp true
```

## Set partitions

The partition table will look different depending on the type of system
and structure. Disk can be listed and partition with `fdisk`.

```bash
fdisk -l
fdisk /dev/[disk]
```

### MBR

| Partition     | Mounting point |
| ------------- | -------------- |
| /dev/[root]   | /              |
| /dev/[swap]   | [SWAP]         |

### GPT

| Partition     | Mounting point |
| ------------- | -------------- |
| /dev/[root]   | /              |
| /dev/[efi]    | /boot          |
| /dev/[swap]   | [SWAP]         |

## Format partitions

Depending on the filesystem, different formats may be used.

```bash
mkfs.ext4 /dev/[root]
mkswap /dev/[swap]
```

## Mount partitions

All the formated partitions must be mounted into their respective 
paths before installation.

```bash
mount /dev/[root] /mnt
swapon /dev/[swap] 

# If UEFI system
mount /dev/[efi] /mnt/boot
```

# Installation

## Mirror selection

By default all the mirrors are enable and ordered by fastest, however
it can be modified in the `/etc/pacman.d/mirrorlist` file.

## Essential packages

To have the minimal CLI Arch Linux system there are only three packages
needed, however more packages are usually needed to have full functionality.

```bash
pacstrap -K /mnt base linux linux-firmware
```

| Functionalities | Packages                          |
| --------------- | --------------------------------- |
| Microcode       | `amd-ucode`, `intel-ucode`        |
| Network         | `networkmanager`, `dhcpcd`        |
| Text editor     | `vi`, `nano`                      |
| Documentation   | `man-db`, `man-pages`             |
| Boot            | `grub`, `efibootmgr`, `os-prober` |
| Filesystem      | `fsck`, `ntfs-3g`                 |

# Configuration

## Filesystem table

In order to keep track of the filesystem structure, it is needed 
to be saved under the new OS with `genfstab`.

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

# Post-installation
