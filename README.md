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
g 
n +1G
n +300G 
n all
t 1
w
q

mkfs.fat -F32 /dev/nvme0n1p1
// Just not put all eggs into btrfs
mkfs.btrfs /dev/nvmen1p2
mkfs.ext4  /dev/nvmen1p3

mount -t btrfs /dev/nvme0n1p2 /mnt

btrfs subvolume create /mnt/@
btrfs subvolume create /mnt/@cache
btrfs subvolume create /mnt/@log
btrfs subvolume create /mnt/@tmp
btrfs subvolume create /mnt/@root
btrfs subvolume create /mnt/@home


chattr +C /mnt/@cache
chattr +C /mnt/@log

umount /mnt
mount -t btrfs -o noatime,nodiratime,ssd,subvol=@ /dev/nvme0n1p2 /mnt

# zsh增加了一个逗号，不要用空格
mkdir -p /mnt/{boot/efi,home,data,root,var/{log,cache,}}

mount -t btrfs -o noatime,nodiratime,ssd,subvol=@log /dev/nvme0n1p2 /mnt/var/log
mount -t btrfs -o noatime,nodiratime,ssd,subvol=@cache /dev/nvme0n1p2 /mnt/var/cache
mount -t btrfs -o noatime,nodiratime,ssd subvol=@root /dev/nvme0n1p2 /mnt/root
mount -t btrfs -o noatime,nodiratime,ssd,subvol=@home /dev/nvme0n1p2 /mnt/home
mount /dev/nvme0n1p3 /mnt/data
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
pacstrap /mnt base base-devel linux linux-firmware dhcpcd vim reflector git networkmanager sudo

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
reboot

systemctl enable NetworkManager
systemctl start NetworkManager

nmcli dev wifi connect <> password <password>

pacman -S xorg xorg-xinit xorg-server awesome

pacman -S terminus-font
vim ~/.xinitrc
xrandr --output eDP-1 --mode aaaxbbb --rate 120 --scale 0.5x0.5
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si

paru google-chrome-stable
paru visual-studio-code-bin
```


