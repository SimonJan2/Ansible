


### kube-master-1

```shell
simonj@kube-master-1:~$ df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              329M  1.1M  328M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   47G  7.1G   38G  16% /
tmpfs                              821M     0  821M   0% /dev/shm
tmpfs                              821M     0  821M   0% /tmp
/dev/sda2                          2.0G  183M  1.7G  11% /boot
none                               1.0M     0  1.0M   0% /run/credentials/getty@tty1.service
tmpfs                              165M  8.0K  165M   1% /run/user/1000
none                               1.0M     0  1.0M   0% /run/credentials/systemd-resolved.service
none                               1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
none                               1.0M     0  1.0M   0% /run/credentials/systemd-networkd.service
simonj@kube-master-1:~$ lsblk -a
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0                       7:0    0    0B  0 loop
loop1                       7:1    0    0B  0 loop
loop2                       7:2    0    0B  0 loop
loop3                       7:3    0    0B  0 loop
loop4                       7:4    0    0B  0 loop
loop5                       7:5    0    0B  0 loop
loop6                       7:6    0    0B  0 loop
loop7                       7:7    0    0B  0 loop
sda                         8:0    0   50G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    2G  0 part /boot
└─sda3                      8:3    0   48G  0 part
  └─ubuntu--vg-ubuntu--lv 252:0    0   48G  0 lvm  /
sdb                         8:16   0   60G  0 disk
sr0                        11:0    1 1024M  0 rom
simonj@kube-master-1:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 26.04 LTS
Release:        26.04
Codename:       resolute
simonj@kube-master-1:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: ens18: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether bc:24:11:f0:46:a0 brd ff:ff:ff:ff:ff:ff
    altname enp0s18
    altname enxbc2411f046a0
    inet 10.100.102.168/24 brd 10.100.102.255 scope global ens18
       valid_lft forever preferred_lft forever
    inet6 fe80::be24:11ff:fef0:46a0/64 scope link proto kernel_ll
       valid_lft forever preferred_lft forever
simonj@kube-master-1:~$ free -mh
               total        used        free      shared  buff/cache   available
Mem:           1.6Gi       431Mi       599Mi       4.9Mi       817Mi       1.2Gi
Swap:          3.8Gi       1.0Mi       3.8Gi
simonj@kube-master-1:~$
```

### kube-master-2

```shell
simonj@kube-master-2:~$ df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              329M  1.1M  328M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   47G  7.1G   38G  16% /
tmpfs                              821M     0  821M   0% /dev/shm
tmpfs                              821M     0  821M   0% /tmp
/dev/sda2                          2.0G  183M  1.7G  11% /boot
none                               1.0M     0  1.0M   0% /run/credentials/getty@tty1.service
tmpfs                              165M  8.0K  165M   1% /run/user/1000
none                               1.0M     0  1.0M   0% /run/credentials/systemd-resolved.service
none                               1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
none                               1.0M     0  1.0M   0% /run/credentials/systemd-networkd.service
simonj@kube-master-2:~$ lsblk -a
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0                       7:0    0    0B  0 loop
loop1                       7:1    0    0B  0 loop
loop2                       7:2    0    0B  0 loop
loop3                       7:3    0    0B  0 loop
loop4                       7:4    0    0B  0 loop
loop5                       7:5    0    0B  0 loop
loop6                       7:6    0    0B  0 loop
loop7                       7:7    0    0B  0 loop
sda                         8:0    0   50G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    2G  0 part /boot
└─sda3                      8:3    0   48G  0 part
  └─ubuntu--vg-ubuntu--lv 252:0    0   48G  0 lvm  /
sdb                         8:16   0   60G  0 disk
sr0                        11:0    1 1024M  0 rom
simonj@kube-master-2:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 26.04 LTS
Release:        26.04
Codename:       resolute
simonj@kube-master-2:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: ens18: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether bc:24:11:d5:bd:09 brd ff:ff:ff:ff:ff:ff
    altname enp0s18
    altname enxbc2411d5bd09
    inet 10.100.102.169/24 brd 10.100.102.255 scope global ens18
       valid_lft forever preferred_lft forever
    inet6 fe80::be24:11ff:fed5:bd09/64 scope link proto kernel_ll
       valid_lft forever preferred_lft forever
simonj@kube-master-2:~$ free -mh
               total        used        free      shared  buff/cache   available
Mem:           1.6Gi       415Mi       609Mi       4.9Mi       811Mi       1.2Gi
Swap:          3.8Gi       1.0Mi       3.8Gi
simonj@kube-master-2:~$
```

### kube-master-3

```shell
simonj@kube-master-3:~$ df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              329M  1.1M  328M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   47G  7.1G   38G  16% /
tmpfs                              821M     0  821M   0% /dev/shm
tmpfs                              821M     0  821M   0% /tmp
/dev/sda2                          2.0G  183M  1.7G  11% /boot
none                               1.0M     0  1.0M   0% /run/credentials/getty@tty1.service
tmpfs                              165M  8.0K  165M   1% /run/user/1000
none                               1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
none                               1.0M     0  1.0M   0% /run/credentials/systemd-resolved.service
none                               1.0M     0  1.0M   0% /run/credentials/systemd-networkd.service
simonj@kube-master-3:~$ lsblk -a
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0                       7:0    0    0B  0 loop
loop1                       7:1    0    0B  0 loop
loop2                       7:2    0    0B  0 loop
loop3                       7:3    0    0B  0 loop
loop4                       7:4    0    0B  0 loop
loop5                       7:5    0    0B  0 loop
loop6                       7:6    0    0B  0 loop
loop7                       7:7    0    0B  0 loop
sda                         8:0    0   50G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    2G  0 part /boot
└─sda3                      8:3    0   48G  0 part
  └─ubuntu--vg-ubuntu--lv 252:0    0   48G  0 lvm  /
sdb                         8:16   0   60G  0 disk
sr0                        11:0    1 1024M  0 rom
simonj@kube-master-3:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 26.04 LTS
Release:        26.04
Codename:       resolute
simonj@kube-master-3:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: ens18: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether bc:24:11:2e:77:23 brd ff:ff:ff:ff:ff:ff
    altname enp0s18
    altname enxbc24112e7723
    inet 10.100.102.170/24 brd 10.100.102.255 scope global ens18
       valid_lft forever preferred_lft forever
    inet6 fe80::be24:11ff:fe2e:7723/64 scope link proto kernel_ll
       valid_lft forever preferred_lft forever
simonj@kube-master-3:~$ free -mh
               total        used        free      shared  buff/cache   available
Mem:           1.6Gi       412Mi       661Mi       4.9Mi       761Mi       1.2Gi
Swap:          3.8Gi       1.1Mi       3.8Gi
simonj@kube-master-3:~$
```

### kube-node-1

```shell
simonj@kube-node-1:~$ df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              680M  1.1M  679M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   47G  7.1G   38G  16% /
tmpfs                              1.7G     0  1.7G   0% /dev/shm
tmpfs                              1.7G     0  1.7G   0% /tmp
/dev/sda2                          2.0G  183M  1.7G  11% /boot
none                               1.0M     0  1.0M   0% /run/credentials/getty@tty1.service
tmpfs                              340M  8.0K  340M   1% /run/user/1000
none                               1.0M     0  1.0M   0% /run/credentials/systemd-resolved.service
none                               1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
none                               1.0M     0  1.0M   0% /run/credentials/systemd-networkd.service
simonj@kube-node-1:~$ lsblk -a
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0                       7:0    0    0B  0 loop
loop1                       7:1    0    0B  0 loop
loop2                       7:2    0    0B  0 loop
loop3                       7:3    0    0B  0 loop
loop4                       7:4    0    0B  0 loop
loop5                       7:5    0    0B  0 loop
loop6                       7:6    0    0B  0 loop
loop7                       7:7    0    0B  0 loop
sda                         8:0    0   50G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    2G  0 part /boot
└─sda3                      8:3    0   48G  0 part
  └─ubuntu--vg-ubuntu--lv 252:0    0   48G  0 lvm  /
sdb                         8:16   0   60G  0 disk
sr0                        11:0    1 1024M  0 rom
simonj@kube-node-1:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 26.04 LTS
Release:        26.04
Codename:       resolute
simonj@kube-node-1:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: ens18: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether bc:24:11:53:40:72 brd ff:ff:ff:ff:ff:ff
    altname enp0s18
    altname enxbc2411534072
    inet 10.100.102.171/24 brd 10.100.102.255 scope global ens18
       valid_lft forever preferred_lft forever
    inet6 fe80::be24:11ff:fe53:4072/64 scope link proto kernel_ll
       valid_lft forever preferred_lft forever
simonj@kube-node-1:~$ free -mh
               total        used        free      shared  buff/cache   available
Mem:           3.3Gi       519Mi       1.5Gi       4.9Mi       1.6Gi       2.8Gi
Swap:          3.8Gi          0B       3.8Gi
simonj@kube-node-1:~$
```

### kube-node-2

```shell
simonj@kube-node-2:~$ df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              680M  1.1M  679M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   47G  7.1G   38G  16% /
tmpfs                              1.7G     0  1.7G   0% /dev/shm
tmpfs                              1.7G     0  1.7G   0% /tmp
/dev/sda2                          2.0G  183M  1.7G  11% /boot
none                               1.0M     0  1.0M   0% /run/credentials/getty@tty1.service
tmpfs                              340M  8.0K  340M   1% /run/user/1000
none                               1.0M     0  1.0M   0% /run/credentials/systemd-resolved.service
none                               1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
none                               1.0M     0  1.0M   0% /run/credentials/systemd-networkd.service
simonj@kube-node-2:~$ lsblk -a
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0                       7:0    0    0B  0 loop
loop1                       7:1    0    0B  0 loop
loop2                       7:2    0    0B  0 loop
loop3                       7:3    0    0B  0 loop
loop4                       7:4    0    0B  0 loop
loop5                       7:5    0    0B  0 loop
loop6                       7:6    0    0B  0 loop
loop7                       7:7    0    0B  0 loop
sda                         8:0    0   50G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    2G  0 part /boot
└─sda3                      8:3    0   48G  0 part
  └─ubuntu--vg-ubuntu--lv 252:0    0   48G  0 lvm  /
sdb                         8:16   0   60G  0 disk
sr0                        11:0    1 1024M  0 rom
simonj@kube-node-2:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 26.04 LTS
Release:        26.04
Codename:       resolute
simonj@kube-node-2:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: ens18: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether bc:24:11:9e:7a:25 brd ff:ff:ff:ff:ff:ff
    altname enp0s18
    altname enxbc24119e7a25
    inet 10.100.102.172/24 brd 10.100.102.255 scope global ens18
       valid_lft forever preferred_lft forever
    inet6 fe80::be24:11ff:fe9e:7a25/64 scope link proto kernel_ll
       valid_lft forever preferred_lft forever
simonj@kube-node-2:~$ free -mh
               total        used        free      shared  buff/cache   available
Mem:           3.3Gi       523Mi       1.5Gi       4.9Mi       1.6Gi       2.8Gi
Swap:          3.8Gi          0B       3.8Gi
simonj@kube-node-2:~$
```

### kube-node-3

```shell
simonj@kube-node-3:~$ df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              680M  1.1M  679M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   47G  7.1G   38G  16% /
tmpfs                              1.7G     0  1.7G   0% /dev/shm
tmpfs                              1.7G     0  1.7G   0% /tmp
/dev/sda2                          2.0G  183M  1.7G  11% /boot
none                               1.0M     0  1.0M   0% /run/credentials/getty@tty1.service
tmpfs                              340M  8.0K  340M   1% /run/user/1000
none                               1.0M     0  1.0M   0% /run/credentials/systemd-resolved.service
none                               1.0M     0  1.0M   0% /run/credentials/systemd-networkd.service
none                               1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
simonj@kube-node-3:~$ lsblk -a
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0                       7:0    0    0B  0 loop
loop1                       7:1    0    0B  0 loop
loop2                       7:2    0    0B  0 loop
loop3                       7:3    0    0B  0 loop
loop4                       7:4    0    0B  0 loop
loop5                       7:5    0    0B  0 loop
loop6                       7:6    0    0B  0 loop
loop7                       7:7    0    0B  0 loop
sda                         8:0    0   50G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    2G  0 part /boot
└─sda3                      8:3    0   48G  0 part
  └─ubuntu--vg-ubuntu--lv 252:0    0   48G  0 lvm  /
sdb                         8:16   0   60G  0 disk
sr0                        11:0    1 1024M  0 rom
simonj@kube-node-3:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 26.04 LTS
Release:        26.04
Codename:       resolute
simonj@kube-node-3:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: ens18: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether bc:24:11:52:c2:64 brd ff:ff:ff:ff:ff:ff
    altname enp0s18
    altname enxbc241152c264
    inet 10.100.102.173/24 brd 10.100.102.255 scope global ens18
       valid_lft forever preferred_lft forever
    inet6 fe80::be24:11ff:fe52:c264/64 scope link proto kernel_ll
       valid_lft forever preferred_lft forever
simonj@kube-node-3:~$ free -mh
               total        used        free      shared  buff/cache   available
Mem:           3.3Gi       508Mi       1.5Gi       4.9Mi       1.6Gi       2.8Gi
Swap:          3.8Gi          0B       3.8Gi
simonj@kube-node-3:~$
```

