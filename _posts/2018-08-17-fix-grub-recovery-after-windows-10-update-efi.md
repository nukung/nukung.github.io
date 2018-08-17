---
layout: post
title: How to fix Archlinux grub error after Windows 10 Update (EFI)
author: Panu Guyson
---

1. Boot with Live USB
2. mount /dev/archlinux_partition /mnt
3. arch-chroot /mnt
4. mount /dev/efi_partition /mnt/boot/efi
5. grub-install /dev/disk
6. grub-mkconfig -o /boot/grub/grub.cfg

exit from chroot and reboot
