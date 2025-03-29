Please note that this is experimental and only for use on non-producion machines by those who have read and understand all this code. This has only been tested on my own system. Chances are extremely high that your system may be borked.

# bcachefs-rollback
This program is a mkinitcpio hook that allows booting into snapshots and rollback on bcachefs

# Installation:

In order to be able to access your snapshots, you must have your snapshots directory at /.snapshots.
DO NOT mount this snapshot directory in /etc/fstab. The hooks will mount it automatically.

.

Copy the bcachefs-rollback hook file to /usr/lib/initcpio/hooks/

Copy the bcachefs-rollback install file to /usr/lib/initcpio/install/

.

Edit /usr/lib/initcpio/hooks/bcachefs-rollback and make sure that the variable LABEL (near the top of the script) is set to your root partition label.

You can find your root partition label by runing the command:
```
blkid
```

Edit /etc/mkinitcpio.conf to include the 'bcachefs-rollback' hook and 'bcachefs' module. Your system's hooks may look different, but just make sure that you add the 'bcachefs-rollback' hook after the 'filesystems' hook:

```
MODULES=(bcachefs)

HOOKS=(base udev autodetect microcode modconf keyboard block filesystems bcachefs-rollback)
```

Update initramfs with:

```
mkinitcpio -P
```


# Running the program:

First check to see if the hook is working by booting up your system and choosing the 'boot root system' menu option. This will boot you into your regular system. If all is okay, make a snapshot for your / drive (the snapshot may be r/o as shown below):

```
bcachefs subvolume snapshot -r / /.snapshots/initial
```

Then restart your computer and choose the 'restore snapshot' option from the booting menu. Type the full word 'initial' (or whatever you've named the snapshot) into the prompt. The system should then take a snapshot of /.snapshots/initial and store it in /@root, which is your new main system. The program will then automatically boot you into /@root. This step should still create a read/write snapshot of /@root even if the 'initial' snapshot was read only.

If all is okay and you'd like to get rid of the residual files on your original / drive, choose the 'delete root system' option in the boot menu. This is experimental, but if everything checks out, all except /.snapshots and /@root should remain.
