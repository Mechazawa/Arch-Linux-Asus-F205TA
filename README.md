# Preparing the ISO
Grab archlinux iso before starting
```sh
# Get the grub source code
# http://www.gnu.org/software/grub/grub-download.html
git clone git://git.savannah.gnu.org/grub.git

cd grub

./autogen.sh

./configure --with-platform=efi --target=i386 --program-prefix=''

make

cd grub-core

../grub-mkimage -d . -o bootia32.efi -O i386-efi -p /boot/grub ntfs hfs 
appleldr boot cat efi_gop efi_uga elf fat hfsplus iso9660 linux 
keylayouts memdisk minicmd part_apple ext2 extcmd xfs xnu part_bsd 
part_gpt search search_fs_file chain btrfs loadbios loadenv lvm minix 
minix2 reiserfs memrw mmap msdospart scsi loopback normal configfile 
gzio all_video efi_gop efi_uga gfxterm gettext echo boot chain eval

# copy bootia32.efi to /tmp directory to be used later
cp bootia32.efi /tmp

# install gptfdisk
pacman -S gptfdisk

# https://github.com/lopaka/instructions/blob/master/ubuntu-14.10-install-asus-x205ta.md
# wipe disk, CHANGE sdb to whatever drive
sgdisk --zap-all /dev/sdb
sgdisk --new=1:0:0 --typecode=1:ef00 /dev/sdb
mkfs.vfat -F32 /dev/sdb1

mount -t vfat /dev/sdb1 /mnt
7z x archlinux-*.iso -o/mnt/

cp /tmp/bootia32.efi /mnt/EFI/boot/

umount /mnt
```

Write this iso to an usb flash drive and boot from it. After that install arch linux 
as you would normally. Please note that your hard disk is NOT `sda` because that is 
your USB flash drive. Nor is it part of `sd*`. Make sure that grub is configured as follows:

## Grub 
```grub
set root=(hd0,gpt1)
linux   /arch/boot/x86_64/vmlinuz archisobasedir=arch archisolabel=archisolabel
initrd  /arch/boot/intel_ucode.img
initrd  /arch/boot/x86_64/archiso.img
boot
```

## Bugs
 - Wifi must never go into idle/sleep or it will not recover. So make sure you're 
constantly doing something network related like `ping 8.8.8.8`.
 - Locks up at random requires a hard reset
 
