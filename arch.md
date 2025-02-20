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

### Wireless network

`iwctl`

Find the wireless device name:

`device list`

If the device or its corresponding adapter is turned off, turn it on:

`device name set-property Powered on`

`adapter adapter set-property Powered on`

Scan for network (the command will not output anything):

`station name scan`

List all available networks:

`station name get-networks`

Connect to a network:

`station name connect SSID`

Exit

`Ctrl + d`

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

`pacstrap -K /mnt base linux-lts linux-firmware git vim`

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

`export LANG=en_US.UTF-8`

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

## NVIDIA

### Install Essential Packages

`sudo pacman -S base-devel linux-lts-headers dkms`

### Install an AUR Helper (e.g., yay)

`git clone https://aur.archlinux.org/yay.git`

`cd yay`

`makepkg -si`

### Enable Multilib Support

`sudo vim /etc/pacman.conf`

You need to find this:

#[multilib]\
#Include = /etc/pacman.d/mirrorlist

Remove the # to uncomment them

[multilib]\
Include = /etc/pacman.d/mirrorlist

Update the package database: `sudo pacman -Syu`

### Install the NVIDIA 470xx Driver Packages from the AUR

`yay -S nvidia-470xx-dkms nvidia-470xx-utils lib32-nvidia-470xx-utils nvidia-470xx-settings`

### Configure DRM Kernel Mode Setting via GRUB

`sudo vim /etc/default/grub`

Find GRUB_CMDLINE_LINUX_DEFAULT and add the nvidia-drm parameters (quiet splash are already present)

`GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nvidia-drm.modeset=1 nvidia-drm.fbdev=1"`

Update GRUB Configuration `sudo grub-mkconfig -o /boot/grub/grub.cfg`

### Configure and Regenerate the Initramfs

`sudo vim /etc/mkinitcpio.conf`

Find the line MODULES=() and modify it to include the nvidia modules

`MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)`

Also, check the HOOKS array and remove kms if it’s present (to avoid loading the nouveau driver too early).

`sudo mkinitcpio -P`

### Reboot the system 

`reboot`

## Verify the NVIDIA Driver Installation

`nvidia-smi`

## i3 setup

`git clone https://github.com/radu-dumitru/arch-linux.git`

`sudo chmod +x setup.sh`

`./setup.sh`

## Safely remove USB drive

Example if my drive is /dev/sdb1

`udisksctl unmount -b /dev/sdb1`

`udisksctl power-off -b /dev/sdb`
