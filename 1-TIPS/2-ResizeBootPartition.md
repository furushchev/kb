# マウントしている領域を無理やりリサイズする

- まずはパーティションをリサイズする

``` bash
$ sudo fdisk /dev/sda

Command (m for help): p

Disk /dev/sda: 2000.4 GB, 2000398934016 bytes
255 heads, 63 sectors/track, 243201 cylinders, total 3907029168 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x000eaa72

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048   454551551   227274752   83  Linux
/dev/sda2       454553598   500117503    22781953    5  Extended
/dev/sda5       454553600   500117503    22781952   82  Linux swap / Solaris

# HDD自体は2TBあるが、パーティションは227GBしか使っていない状態
# 現在のパーティションテーブルを一度削除する

Command (m for help): d
Partition number (1-5): 2

Command (m for help): d
Partition number (1-5): 1

Command (m for help): p

Disk /dev/sda: 2000.4 GB, 2000398934016 bytes
255 heads, 63 sectors/track, 243201 cylinders, total 3907029168 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x000eaa72

   Device Boot      Start         End      Blocks   Id  System

# まっさらなパーティションテーブルになっている。
# ここに新しくパーティションを作る
# First sectorが削除前のテーブルと同じことを確認する　（重要）
Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-3907029167, default 2048): 
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-3907029167, default 3907029167): 3907029167

Command (m for help): p

Disk /dev/sda: 2000.4 GB, 2000398934016 bytes
255 heads, 63 sectors/track, 243201 cylinders, total 3907029168 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x000eaa72

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1            2048  3907029167  1942122583+  83  Linux

# パーティションのIdも同じであることを確認

# 一応Bootフラグも元に戻しておく
Command (m for help): a
Partition number (1-5): 1

# パーティションテーブルの変更を反映
Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
```

- パーティションデーブルの更新を反映させる

{% term %}
sudo reboot
{% endterm %}

- ファイルシステムのりサイズ

```bash
# この状態ではまだ領域が拡張されていない
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            7.8G  8.0K  7.8G   1% /dev
tmpfs           1.6G  1.6M  1.6G   1% /run
/dev/sda1       214G  196G  7.3G  97% /
none            4.0K     0  4.0K   0% /sys/fs/cgroup
none            5.0M  8.0K  5.0M   1% /run/lock
none            7.8G   80K  7.8G   1% /run/shm
none            100M   32K  100M   1% /run/user

# ファイルシステムのサイズ変更
$ sudo resize2fs /dev/sda1
[sudo] password for furushchev: 
resize2fs 1.42.9 (4-Feb-2014)
Filesystem at /dev/sda1 is mounted on /; on-line resizing required
old_desc_blocks = 14, new_desc_blocks = 116
The filesystem on /dev/sda1 is now 485530645 blocks long.

# 増えた
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            7.8G  8.0K  7.8G   1% /dev
tmpfs           1.6G  1.6M  1.6G   1% /run
/dev/sda1       1.8T  196G  1.6T  12% /
none            4.0K     0  4.0K   0% /sys/fs/cgroup
none            5.0M  4.0K  5.0M   1% /run/lock
none            7.8G   88K  7.8G   1% /run/shm
none            100M   32K  100M   1% /run/user
```

