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

### EFI Partition

`n` - add a new partition

`+1G`

### SWAP Partition

`n`

`+8G`

### Root Partition

`n`

Use the default settings

### Change the EFI partition type

`t`

`1` 

`1` 

### Change the SWAP partition type

`t`

`2`

`19`

### Write the table to the disk and exit

`w`

## Format the partitions

`mkfs.fat -F32 /dev/sda1` - the EFI partition

`mkswap /dev/sda2` - the SWAP partition

`swapon /dev/sda2`

`mkfs.ext4 /dev/sda3` - the root partition

## Mount the file systems

`mount /dev/sda3 /mnt`

`mkdir /mnt/boot`

`mount /dev/sda1 /mnt/boot`





