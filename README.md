Please note that this is complelety experimental and only for use on non-producion machines.

# bcachefs-rollback
This program is a mkinitcpio hook that allows booting into snapshots and rollback on bcachefs

# Installation:

In order to be able to access your snapshots, you must have your snapshots directory at /.snapshots.
DO NOT mount this snapshot directory in /etc/fstab. The hooks will mount it automatically.

.

Copy the bcachefs-rollback hook file to /usr/lib/initcpio/hooks/

Copy the bcachefs-rollback install file to /usr/lib/initcpio/install/

.

Edit /usr/lib/initcpio/hooks/bcachefs-rollback and make sure that the variable LABEL is set to your root partition label

You can find your root partition label by runing the command: blkid

.

Edit /etc/mkinitcpio.conf to include the 'bcachefs-rollback' hook and 'bcachefs' module:

MODULES=(bcachefs)

HOOKS=(base udev autodetect microcode modconf keyboard block filesystems bcachefs-rollback)

.

Update initramfs with:

mkinitcpio -P

# Running the program:

In order for you to make be able to use this program you must first make a snapshot for your / drive (the snapshot may be r/o as shown below):

bcachefs subvolume snapshot -r / /.snapshots/initial

Then restart your computer and choose the 'restore snapshot' option from the booting menu. Type the full word 'initial' (or whatever you've named the snapshot) into the prompt. The system should then take a snapshot of /.snapshots/initial and store it in /@root, which is your new main system. The program will then automatically boot you into /@root. This step should still create a read/write snapshot of /@root even if the 'initial' snapshot was read only.

Hopefully things went fine.
