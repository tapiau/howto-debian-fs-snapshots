# howto-debian-fs-snapshots
Debian FS snapshots and vanilia state restore


Do debian clean install. 

```
mkdir /mnt/btrfs
mount /dev/sda1 /mnt/btrfs -o subvol=/
btrfs subvol create /mnt/btrfs/@snap
btrfs subvol create /mnt/btrfs/@boot
cp -ar /boot/* /mnt/btrfs/@boot/
```

Add to /etc/fstab:
```
UUID=filesystem-uuid-same-as-for-rootfs /boot               btrfs   noatime,nodiratime,subvol=@boot 0       0
UUID=filesystem-uuid-same-as-for-rootfs /mnt/btrfs               btrfs   noatime,nodiratime,subvol=/ 0       0
```

Reboot.

```
grub-install /dev/sda
btrfs subvol snapshot -r /mnt/btrfs/@rootfs/ /mnt/btrfs/_init
```

Put ```restore.sh``` into /mnt/btrfs/
```
#!/bin/bash

NOW=`date +'%Y%m%d-%H%M%S'`

mv /mnt/btrfs/@rootfs /mnt/btrfs/@snap/rootfs-${NOW}-rw
btrfs subvol snapshot /mnt/btrfs/_init /mnt/btrfs/@rootfs
```

put ```shapshot.sh``` into /mnt/btrfs/
```
#!/bin/bash

NOW=`date +'%Y%m%d-%H%M%S'`

btrfs subvol snapshot -r /mnt/btrfs/@rootfs /mnt/btrfs/@snap/rootfs-${NOW}-ro
```
