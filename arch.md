## Verify the boot mode

`cat /sys/firmware/efi/fw_platform_size`

64 - the system is booted in UEFI mode and has a 64-bit x64 UEFI\
32 - the system is booted in UEFI mode and has a 32-bit IA32 UEFI

## Check internet connection

`ping www.google.com`

## Update the system clock

`timedatectl set-ntp true`\
`timedatectl status`

## Partition the disks

`lsblk`

`fdisk /dev/sda`

`g` - create a GPT partition table

Use `p` to check the partition layout

### EFI Partition

`n` => `+1G`

### SWAP Partition

`n` => `+8G`

### Root Partition

`n` => use the default settings

### Change the EFI partition type

`t` => `1` => `1` 

### Change the SWAP partition type

`t` => `2` => `19`

### Write the table to the disk and exit using `w`

## Format the partitions

`mkfs.fat -F32 /dev/sda1` - the EFI partition

`mkswap /dev/sda2` - the SWAP partition

`swapon /dev/sda2`

`mkfs.ext4 /dev/sda3` - the root partition

## Mount the file systems

`mount /dev/sda3 /mnt`

`mkdir /mnt/boot`

`mount /dev/sda1 /mnt/boot`

## Install essential packages

`pacstrap -K /mnt base linux linux-firmware vim`

## Fstab

`genfstab -U /mnt >> /mnt/etc/fstab`

## Chroot

`arch-chroot /mnt`

## Time

Check the Region and City by looking in the `/user/share/zoneinfo/` directory and then update the following command

`ln -sf /usr/share/zoneinfo/Region/City /etc/localtime`

`hwclock --systohc`

## Localization

`vim /etc/locale.gen`

Uncomment `en_US.UTF-8` and save the file

`locale-gen`

`echo LANG=en_US.UTF-8 > /etc/locale.conf`

`export LANG=EN_US.UTF-8`

## Create the hostname

`vim /etc/hostname` => archlinux and save

## Root password

`passwd`

## Add a normal user

`useradd -m username` vs `useradd -m -g users -G wheel,storage,power -s /bin/bash username`

`passwd username`

`usermod -aG wheel,audio,video,optical,storage username`

`pacman -S sudo`

`visudo` => uncomment the line `%wheel ALL=(ALL:ALL) ALL` to allow members of the wheel group to execute any command
