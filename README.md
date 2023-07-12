# Arch Linux Installation Script

This script automates the installation process of Arch Linux with UEFI on your system. It includes disk partitioning, package installation, system configuration, and post-install tweaks.

## Usage

1. Boot into the Arch Linux installation media.

2. Partition the disk using `fdisk`:
   ```bash
   fdisk /dev/sda

# Create the file systems and mount the root partition:

mkfs.fat -F32 /dev/sda1
pvcreate --dataalignment 1m /dev/sda2
vgcreate vg0 /dev/sda2
lvcreate -l 100%FREE vg0 -n lv0
modprobe dm_mod
vgscan
mkfs.ext4 /dev/vg0/lv0
mount /dev/vg0/lv0 /mnt
mkdir /mnt/etc
genfstab -U -p /mnt >> /mnt/etc/fstab

# Install Arch Linux:

pacstrap -i /mnt base
arch-chroot /mnt

# Install GRUB:

pacman -S grub efibootmgr dosfstools os-prober mtools
mkdir /boot/EFI
mount /dev/sda1 /boot/EFI
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck

# Perform post-install teaks:

cd /root
dd if=/dev/zero of=/swapfile bs=1M count=4096 status=progress
chmod 600 /swapfile
mkswap /swapfile
cp /etc/fstab /etc/fstab.bk
echo '/swapfile none swap sw 0 0' | tee -a /etc/fstab

# Reboot and enjoy your newly installed Arch Linux system!

# Disclaimer
# Please note that running this script will modify your system. Use it at your own risk. Ensure that you have a backup of your important data before proceeding with the installation.
