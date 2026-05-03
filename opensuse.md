# OpenSuse

## Recovering after a Windows Update

1. Boot into a live CD/USB.
1. Use lsblk to find the partition
1. Mount the root partition: `mount /dev/nvme0n1p9 /mnt`
2. Mount the efi partition: `mount /dev/nvme0n1p9 /mnt/efi`
3. List the contents of fstab and use the mappings to find the remaining btrfs subvolumes: `cat /mnt/etc/fstab`
4. (Optional) Mount the subvolumes:
    1. `mount /dev/nvme0n1p9 -o subvol=/@/home /mnt/home`
    2. `mount /dev/nvme0n1p9 -o subvol=/@/var /mnt/var`
    3. ...
5. Mount the boot subdirectories:
    1. `mount /dev/nvme0n1p9 -o subvol=/@//boot/grub2/x86_64-efi /mnt/boot/grub2/x86_64-efi`
    2. `mount /dev/nvme0n1p9 -o subvol=/@//boot/grub2/i386-pc /mnt/boot/grub2/i386-pc`
6. Mount the efivarfs: `mount -t efivarfs efivarfs /mnt/sys/firmware/efi/efivars`
7. Mount the devices from the live image: `for i in /dev /dev/pts /proc /sys /run; do mount -B $i /mnt/$i; done`
8. Chroot into the OpenSuse install: `chroot /mnt`
9. Reinstall GRUB files: `grub2-install`
10. Refresh the boot menu configuration: `grub2-mkconfig -o /boot/grub2/grub.cfg`
11. Force an update to the EFI boot manager: `shim-install`
12. Exit and reboot
    1. `exit`
    2. `umount -R /mnt`
    3. `reboot`
