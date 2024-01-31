# yoko-arch
Install and config

# 分区方案

1GB efi 分区 /boot
100GB root根 分区 /
100GB root用户分区 /root
100GB home用户 /home
restofall data /data

```
fdisk -l /dev/nvme0n1 
```
