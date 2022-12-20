# How to Find the Date/Time Ubuntu Linux Was Installed

There are a few ways to find the installation timestamp of an Ubuntu OS.

## 1. Stat Command

The `stat` command can be used to display the status of a file or file system. When used on the root `/` filesystem, it *may* contain the Birth timestamp.

    user@ubuntu:~$ stat /
      File: /
      Size: 4096      	Blocks: 8          IO Block: 4096   directory
    Device: 805h/2053d	Inode: 2           Links: 20
    Access: (0755/drwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
    Access: 2022-12-21 06:30:16.948541982 +1030
    Modify: 2022-12-21 06:18:58.337480354 +1030
    Change: 2022-12-21 06:18:58.337480354 +1030
     Birth: 2022-11-23 14:26:18.000000000 +1030


## 2. Filesystem Creation Date

The `tune2fs` command can be used to display the current values of the tunable options of a filesystem, including the created date.

Firstly, determine the device the root filesystem is mounted on:

    user@ubuntu:~$ df /
    Filesystem     1K-blocks     Used Available Use% Mounted on
    /dev/sda5      131006500 10653704 113651896   9% /

In this example, the root filesystem is installed on `/dev/sda5`. Then use that value with the `tune2fs` command.

    user@ubuntu:~$ sudo tune2fs -l /dev/sda5
    tune2fs 1.46.5 (30-Dec-2021)
    Filesystem volume name:   <none>
    Last mounted on:          /
    Filesystem UUID:          e8b5a02d-6deb-43d6-a1d2-d4ce84743918
    Filesystem magic number:  0xEF53
    Filesystem revision #:    1 (dynamic)
    ...
    Filesystem created:       Wed Nov 23 14:26:18 2022
    ...


## 3. Install Logs

Installation logs are not very reliable as they are often one of the first files to be deleted when trying to reclaim space. However, if present, they can also reveal the install date.

    user@ubuntu:~$ sudo head -n1 /var/log/installer/syslog
    Nov 23 14:25:37 ubuntu systemd-sysusers[3313]: Creating group systemd-coredump with gid 998.

Install logs may also reveal if the Ubuntu OS was upgraded. The presence of the `/var/log/dist-upgrade` directory strongly indicates an upgrade occurred.

    user@ubuntu:~$ sudo head -n1 /var/log/dist-upgrade/main.log
    2022-12-21 05:50:20,157 INFO Using config files '['./DistUpgrade.cfg.focal', '/etc/update-manager/release-upgrades.d/ubuntu-advantage-upgrades.cfg']'

The following command might also reveal the previous installed version that was upgraded:

    user@ubuntu:$ grep INFO.uname main.log
    2022-12-21 05:50:20,158 INFO uname information: 'Linux ubuntu 5.15.0-56-generic #62~20.04.1-Ubuntu SMP Tue Nov 22 21:24:20 UTC 2022 x86_64'
