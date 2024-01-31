# yoko-arch
Install and config

# 分区方案

1GB efi 分区 /boot
100GB root根 分区 /
100GB root用户分区 /root
100GB home用户 /home
restofall data /data

```
fdisk /dev/nvme0n1
p
n +1G
n +100G
n +100G
n +100G
n all
w
q

mkfs.fat -F32 /dev/nvme0n1p1
// Just not put all eggs into btrfs
mkfs.btrfs -f -m single -L "Root" /dev/nvmen1p2
mkfs.btrfs -f -m single -L "Root User" /dev/nvmen1p3
mkfs.btrfs -f -m single -L "User" /dev/nvmen1p4
mkfs.btrfs -f -m single -L "Data" /dev/nvmen1p5

mount -t btrfs -o compress=lzo /dev/nvme0n1p2 /mnt

btrfs subvolume create /mnt/@
btrfs subvolume create /mnt/@cache
btrfs subvolume create /mnt/@log
btrfs subvolume create /mnt/@tmp

chattr +C /mnt/@cache
chattr +C /mnt/@log

umount /mnt
mount -o noatime,nodiratime,ssd,compress=lzo,subvol=@ /dev/nvme0n1p2 /mnt

# zsh增加了一个逗号，不要用空格
mkdir -p /mnt/{boot/efi,home,data,root,var/{log,cache,}}

mount -o noatime,nodiratime,ssd,compress=lzo,subvol=@log /dev/nvme0n1p2 /mnt/var/log
mount -o noatime,nodiratime,ssd,compress=lzo,subvol=@cache /dev/nvme0n1p2 /mnt/var/cache

mount -o noatime,nodiratime,ssd,compress=lzo /dev/nvme0n1p3 /mnt/root
mount -o noatime,nodiratime,ssd,compress=lzo /dev/nvme0n1p4 /mnt/home
mount -o noatime,nodiratime,ssd,compress=lzo /dev/nvme0n1p5 /mnt/data

mount /dev/nvme0n1p1 /mnt/boot/efi


iwctl
device list
station wlan0 scan
station wlan0 get-networks
station wlan0 connect wifi-name
exit

timedatectl set-ntp true
timedatectl status


vim /etc/pacman.conf
ParallelDownloads = 5

pacman -Sy archlinux-keyring # 镜像老了（该换新的了
pacstrap /mnt base base-devel linux linux-firmware dhcpcd vim reflector git iw

genfstab -L /mnt >> /mnt/etc/fstab

arch-chroot /mnt
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc

vim /etc/locale.gen
# uncomment /etc/locale.gen

locale-gen

vim /etc/hostname
# superyoko

passwd

useradd -m -G wheel yoko
passwd yoko

vim /etc/sudoers
# enable sudoer # uncomment wheel line

pacman -S intel-ucode
pacman -S grub efibootmgr

grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=grub
grub-mkconfig -o /boot/grub/grub.cfg

```


