# bcachefs-snapshot-hook
A hook to allow booting into snapshots and rollback on bcachefs

# Installation:

In order to be able to access your snapshots, you must have your snapshots directory at /.snapshots.
Do NOT mount this snapshot directory in /etc/fstab. The hooks will mount it automatically.

.

Copy the bcachefsroot hook file to /usr/lib/initcpio/hooks/

Copy the bcachefsroot install file to /usr/lib/initcpio/install/

.

Edit /etc/mkinitcpio.conf to include the 'bcachefsroot' hook and 'bcachefs' module:

MODULES=(bcachefs)

HOOKS=(base udev autodetect microcode modconf keyboard block bcachefsroot filesystems)

.

Update initramfs with:

mkinitcpio -P

