# bcachefs-snapshot-hook
A hook to allow booting into snapshots and rollback on bcachefs

# Installation:

In order to be able to access your snapshots, you must have your snapshots directory at /.snapshots.
DO NOT mount this snapshot directory in /etc/fstab. The hooks will mount it automatically.

.

Copy the bcachefsroot hook file to /usr/lib/initcpio/hooks/

Copy the bcachefsroot install file to /usr/lib/initcpio/install/

.

Edit /usr/lib/initcpio/hooks/bcachefsroot and make sure that the variable LABEL is set to your root partition label

You can find your root partition label by runing the command: blkid

.

Edit /etc/mkinitcpio.conf to include the 'bcachefsroot' hook and 'bcachefs' module:

MODULES=(bcachefs)

HOOKS=(base udev autodetect microcode modconf keyboard block bcachefsroot filesystems)

.

Update initramfs with:

mkinitcpio -P

