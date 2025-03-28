# bcachefs-hook
A hook to allow booting into snapshots and rollback on bcachefs

# Installation:
Copy the bcachefsroot hook file to /usr/lib/initcpio/hooks/
Copy the bcachefsroot install file to /usr/lib/initcpio/install/
Edit /etc/mkinitcpio.conf to include the 'bcachefsroot' hook and 'bcachefs' module:

MODULES=(bcachefs)
HOOKS=(base udev autodetect microcode modconf keyboard block bcachefsroot filesystems)

Update initramfs with:
mkinitcpio -P

