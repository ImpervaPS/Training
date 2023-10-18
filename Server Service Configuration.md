## FTP Service
check the link <https://computingforgeeks.com/install-and-configure-ftp-server-on-windows-server-2019/>

## Resize Disk

Check the current disk space
```
[user@dsfhub413 ~]$ df -h
Filesystem             Size  Used Avail Use% Mounted on
devtmpfs                16G     0   16G   0% /dev
tmpfs                   16G     0   16G   0% /dev/shm
tmpfs                   16G   18M   16G   1% /run
tmpfs                   16G     0   16G   0% /sys/fs/cgroup
/dev/mapper/rhel-root   70G  5.4G   65G   8% /
/dev/sda2             1014M  259M  756M  26% /boot
/dev/mapper/rhel-home  163G  4.7G  158G   3% /home
/dev/sda1              599M  5.8M  594M   1% /boot/efi
tmpfs                  3.2G   12K  3.2G   1% /run/user/42
tmpfs                  3.2G  4.0K  3.2G   1% /run/user/1000
```

Clearly we don't have enough space in root. So the plan is to allocate 140G from /dev/mapper/rhel-home to /dev/mapper/rhel-root.

Run the command using root in this order:
```
--01-backup the /home folder
tar -cvf /tmp/home.tar /home

--02-umount /home
cd /tmp
umount /home

--02b-error
[root@dsfhub413 /]# umount /home
umount: /home: target is busy.

```

if you see above error, run the command:
```
[root@dsfhub413 /]# fuser -km /home/
/home:                2469m  2483c  2545c  2551c
Connection to 192.168.x.x closed.
```

You need to login using the root account again.
run the umount command again:
```
[root@dsfhub413 tmp]# df -h
Filesystem             Size  Used Avail Use% Mounted on
devtmpfs                16G     0   16G   0% /dev
tmpfs                   16G     0   16G   0% /dev/shm
tmpfs                   16G   18M   16G   1% /run
tmpfs                   16G     0   16G   0% /sys/fs/cgroup
/dev/mapper/rhel-root   70G  8.8G   62G  13% /
/dev/sda2             1014M  259M  756M  26% /boot
/dev/sda1              599M  5.8M  594M   1% /boot/efi
tmpfs                  3.2G   12K  3.2G   1% /run/user/42
tmpfs                  3.2G     0  3.2G   0% /run/user/0
```

the /home folder is gone.
```
[root@dsfhub413 tmp]# lvremove /dev/mapper/rhel-home
Do you really want to remove active logical volume rhel/home? [y/n]: y
  Logical volume "home" successfully removed.
[root@dsfhub413 tmp]# lvextend -L +140G /dev/mapper/rhel-root
  Size of logical volume rhel/root changed from 70.00 GiB (17920 extents) to 210.00 GiB (53760 extents).
  Logical volume rhel/root successfully resized.
[root@dsfhub413 tmp]# xfs_growfs /dev/mapper/rhel-root
meta-data=/dev/mapper/rhel-root  isize=512    agcount=4, agsize=4587520 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=18350080, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=8960, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 18350080 to 55050240

```

We will add 22GB to the /home folder. We arrived at this by calculating 163GB minus 140GB, which equals 23GB. However, to be on the safe side, we're allocating 1GB less.

```
[root@dsfhub413 tmp]# lvcreate -L 22G -n /dev/mapper/rhel-home
  Logical volume "home" created.
[root@dsfhub413 tmp]# mkfs.xfs /dev/mapper/rhel-home
meta-data=/dev/mapper/rhel-home  isize=512    agcount=4, agsize=1441792 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=5767168, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2816, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@dsfhub413 tmp]# mount /dev/mapper/rhel-home
[root@dsfhub413 tmp]# df -h
Filesystem             Size  Used Avail Use% Mounted on
devtmpfs                16G     0   16G   0% /dev
tmpfs                   16G     0   16G   0% /dev/shm
tmpfs                   16G   18M   16G   1% /run
tmpfs                   16G     0   16G   0% /sys/fs/cgroup
/dev/mapper/rhel-root  210G  9.8G  201G   5% /
/dev/sda2             1014M  259M  756M  26% /boot
/dev/sda1              599M  5.8M  594M   1% /boot/efi
tmpfs                  3.2G   12K  3.2G   1% /run/user/42
tmpfs                  3.2G     0  3.2G   0% /run/user/0
/dev/mapper/rhel-home   22G  190M   22G   1% /home

```

restore the /home folder:
```
[root@dsfhub413 /]# tar -xvf /tmp/home.tar -C /
home/
home/user/
```

And you can run again df -h to check the result.
```
[user@dsfhub413 ~]$ df -h
Filesystem             Size  Used Avail Use% Mounted on
devtmpfs                16G     0   16G   0% /dev
tmpfs                   16G     0   16G   0% /dev/shm
tmpfs                   16G   18M   16G   1% /run
tmpfs                   16G     0   16G   0% /sys/fs/cgroup
/dev/mapper/rhel-root  210G  9.8G  201G   5% /
/dev/sda2             1014M  259M  756M  26% /boot
/dev/sda1              599M  5.8M  594M   1% /boot/efi
/dev/mapper/rhel-home   22G  3.7G   19G  17% /home
tmpfs                  3.2G   12K  3.2G   1% /run/user/42
tmpfs                  3.2G     0  3.2G   0% /run/user/0
```