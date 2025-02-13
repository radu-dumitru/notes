## Verify signature

Download the ISO PGP signature file from https://archlinux.org/download/

Create a sha.txt file with the following content:

`SHA256 (from https://archlinux.org/download/) archlinux-version-x86_64.iso`

All the files (iso image, iso.sig file, sha.txt) need to be in the same folder

Execute this command: `sha256sum -c sha.txt` 

Check the ISO PGP signature by executing this command:

`gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig`

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

`useradd -m username` 

`passwd username`

`usermod -aG wheel,audio,video,optical,storage,power username`

`pacman -S sudo`

`visudo` => uncomment the line `%wheel ALL=(ALL:ALL) ALL` to allow members of the wheel group to execute any command

## Install grub

`pacman -S grub`

`pacman -S efibootmgr dosfstools os-prober mtools`

`grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB`

`grub-mkconfig -o /boot/grub/grub.cfg`

## Install a network manager

`pacman -S networkmanager`

`systemctl enable NetworkManager.service`

## Exit from chroot

`exit`

## Unmount

`umount -R /mnt`

## Restart the machine

`reboot` (for a vm use `shutdown now` and remove the installation image)
