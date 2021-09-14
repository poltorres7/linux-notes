Installation commands
=======

This README has the notes from the learning process in the installation of arch linux

Documentation Links
- https://wiki.archlinux.org/title/Installation_guide
- https://wiki.archlinux.org/title/Iwd#iwctl 
- 


#### BIOS installation

##### Connect to internet

Use iwctl to connect via wireless

```bash
# list hardware devices
device list

# Scan for networks
device wlan0 scan

# Get networks in your range
device wlan0 get-networks

# Connect to your Network
device wlan0 connect SSID

# Confirm connection
ping 8.8.8.8
```

##### Update System Clock

```bash
timedatectl set-ntp true

timedatectl status
```

##### Create the partitions

In this notes we used an MBR layout

```bash
# List disks
fdisk -l

# Modify a disk, where X is the ID of your disk
fdisk /dev/sdX

# print current partitions
p

# create the swap partition
n                         # create a new partition
1                         # select the partition ID
press enter (default one) # select the initial sector
+2048M                    # specify the partition size


# create the OS partition
n                           # create new partition
2                           # partition ID
press enter (default one)   # select the initial sector
press enter (default one)   # this will use the rest of the free space
```

**Format partitions**

```bash
mkfs.ext4 /dev/sdX2  # this is for the OS system

mkswap /dev/sdX1     # this is for the swap memory
```

##### Mount File Systems

```bash
mount /dev/sdX2 /mnt
swapon /dev/sdX1
```

##### Install essential packages

```bash
pacstrap /mnt base linux linux-firmware
```

> Avoid `linux-firmware` in containers and VMs

##### Configure the system

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

##### Chroot

```bash
arch-chroot /mnt
```

##### Install basic Software

```bash
pacman -S vim grub
```

##### Time Zone

```bash
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
hwclock --systohc
```

##### Localization

Edit `/etc/locale.gen` and uncomment `en_US.UTF-8 UTF-8` and other needed locales. Generate the locales by running: 

```bash
locale-gen
```

Edit `/etc/vconsole.conf`

```bash
KEYMAP=us
```

##### Network configuration

Create hostname `/etc/hostname`

```bash
myhostname
```

Create `/etc/hosts`

```bash
127.0.0.1	localhost
::1		    localhost
127.0.1.1	myhostname.localdomain	myhostname
```

##### Initframs

```bash
mkinitcpio -P
```

##### Root password

```bash
passwd
```

##### Install the grub

```bash
grub-install --recheck --target=i386-pc /dev/sda

grub-mkconfig -o /boot/grub/grub.cfg
```

Reference: https://www.addictivetips.com/ubuntu-linux-tips/re-install-grub-on-arch-linux/

