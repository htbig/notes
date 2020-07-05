# openwrt fs mount 
```
source code :stools-2018-04-16-e2436836/mount_root.c
Called in the early (PREINIT) stage, when we immediately need some writable
filesystem.
mount("/dev/root", "/", NULL, MS_NOATIME | MS_REMOUNT, 0);
mount_overlay()
fopivot("/overlay", "/rom")
fopivot - switch to overlay using passed dir as upper one
```
# why bin file can't upgrade
```
mmc patitioned into p1 p2 p3
p1 and p2 are rootfs,p3 is the config save patition
the '/' is /dev/p1 or /dev/p2,
overlayfs:/overlay / overlay rw,noatime,lowerdir=/,upperdir=/overlay/upper,workdir=/overlay/work 0 0
so / is the /dev/p1(p2) and /overlay/upper's merge dir
when sysupgrade,the p1 or p2 is update, and the file is the newer,but when you use opkg install the package,then the bin file will changed,
then the /overlay/upper layer will have the changed file, so after sysupgrade, the lowerdir file is the newerst, but the upper dir is the old,
when upper dir has the same file,the lower dir file will be hidden.
```
