It is highly recommended that you read all of this information before proceeding!

Please note that this is experimental and only for use on non-producion machines. Do not run this program unless you have read and understand all the code within. This has only been tested on my own system, and I am not a professional coder. Chances are high that your system may become borked by running this.

I made this script because I was unable to find anything else online that allowed a person to easily do rollbacks and/or boot into snapshots on bcachefs. It would be great to get the ball rolling, even if this is not 'good quality' code to start with. My hope is that others will use/modify this program and report back with the changes so all can benefit.

Enjoy! :)

# bcachefs-rollback
This program is a mkinitcpio hook that allows booting into snapshots and rollback on bcachefs

# Features:

- Easily boot snapshots, main root /, @root, or another directory
- Recover root snapshots instantly
- Mount any boot circumstance in tmpfs or overlay mode (squashfs functionality coming soon!)
- Turn a r/o snapshot into a r/w snapshot on the fly

# Installation:

I should mention that I'm not entirely sure how flexible this program is with certain subvolume, boot, and efi setups.

For reference, here is what my /etc/fstab looks like:


```
# /dev/nvme0n1p4 LABEL=ROOT-laptop
UUID=9255082a-98bf-40b3-b26b-b79a1096cc4b       /               bcachefs        rw,noatime      0 0

# /dev/nvme0n1p2 LABEL=BOOT
UUID=63600023-1a46-4983-b17f-9487092fba31       /boot           ext2            rw,noatime      0 2

# /dev/nvme0n1p1 LABEL=EFI
UUID=42C6-79ED          /efi            vfat            rw,noatime,fmask=0022,dmask=0022,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro        0 2

#/.snapshots                /.snapshots          none            rw,noatime,rw,noshard_inode_numbers,bind  0 0

/var/log                /var/log          none            rw,noatime,rw,noshard_inode_numbers,bind  0 0

/var/tmp                /var/tmp          none            rw,noatime,rw,noshard_inode_numbers,bind  0 0
```

In order to be able to access your snapshots, you must have your snapshots directory at /.snapshots or change the snapshot directory path in your bcachefs-rollback hooks file.
DO NOT mount this snapshot directory in /etc/fstab. The hooks will mount it automatically.

.

Copy the bcachefs-rollback hooks file to /usr/lib/initcpio/hooks/

Copy the bcachefs-rollback install file to /usr/lib/initcpio/install/

.

Edit /etc/mkinitcpio.conf to include the 'bcachefs-rollback' hook and 'bcachefs' module. Your system's hooks may look different, but just make sure that you add the 'bcachefs-rollback' hook after the 'filesystems' hook (I'm not 100% sure where this hook should go, but this seems to work for me):

```
MODULES=(bcachefs)

HOOKS=(base udev autodetect microcode modconf keyboard block filesystems bcachefs-rollback)
```

Update initramfs with:

```
mkinitcpio -P
```


# Setting it up:

First check to see if the hook is working by booting up your system. It should look close to this:

```
What would you like to do?

<s> boot into a snapshot
<r> restore snapshot
<b> boot root system
<n> create @root snapshot from /
<d> delete root system
<c> boot custom dir
<e> enter bash

<w> add ro -> rw flag
<o> add overlay flag
<t> add tmpfs flag
<m> add custom mount opts (Not working)
<z> add squashfs flag (TODO)

<ENTER> boot @root
```

First boot into your regular root drive (/) by selecting 'boot root system'. Make sure your computer is working as it should. If all is well, restart your computer and choose the 'create @root snapshot from /' option from the booting menu. This will create a snapshot of your root system (ie., /) called '/@root', which will for now on be where your main system will be located. Such a setup will allow for easy rollback functionality and other features. Press ENTER to boot into the @root subvolume.

# Running the program:

Having created the @root subvolume, bcachefs-rollback will automatically boot into it if the menu is allowed to time out (after 2 seconds) or if the ENTER key is pressed whilst in the menu.

If the @root subvolume boots without error, you may want to delete the residual files on your original / drive. Do so by choosing the 'delete root system' option. If everything checks out, all except /.snapshots and /@root should remain. At this point all your system files will be in /@root.

# Extra features

You may wish to boot into an overlay filesystem if you're booting a read only subvolume. Simply select the 'add overlay flag' and then make your selection of what to boot. The same goes for the 'add tmpfs flag' option. That said, it should be known that these modes have no effect on separate partitions, such as /boot and /efi. As of now, it is not possible to take snapshots in tmpfs and overlay mode.

For a more permanent solution, choose the 'add ro -> rw flag' if you'd like to transform a read only snapshot to a read/write snapshot.

# TODO

- Find a way to easily select snapshots within the menu. Currently, they must be fully typed out.
- Implement squashfs functionality

# Bugs

- The first restore (before /@root is created) will result in a small error when the system attempts to move a non-existant backup snapshot of /@root to /.snapshots [FIXED]
- Custom mount opts not working. Could be overriden by /etc/fstab?
- Label has to be manually entered. [FIXED - now it's automatic]
