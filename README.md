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
n +32G swap
n +50 /backup
n all
t 1
w
q

mkfs.fat -F32 /dev/nvme0n1p1
// Just not put all eggs into btrfs
mkfs.btrfs /dev/nvmen1p2
mkswap  /dev/nvmen1p3
mkfs.btrfs /dev/nvmen1p4
mkfs.ext4  /dev/nvmen1p5

mount -t btrfs /dev/nvme0n1p2 /mnt

btrfs subvolume create /mnt/@
btrfs subvolume create /mnt/@cache
btrfs subvolume create /mnt/@log
btrfs subvolume create /mnt/@home


chattr +C /mnt/@cache
chattr +C /mnt/@log

umount /mnt
mount -t btrfs -o noatime,nodiratime,ssd,subvol=@ /dev/nvme0n1p2 /mnt

# zsh增加了一个逗号，不要用空格
mkdir -p /mnt/{boot/efi,backup,home,data,var/{log,cache,}}

mount -t btrfs -o noatime,nodiratime,ssd,subvol=@log /dev/nvme0n1p2 /mnt/var/log
mount -t btrfs -o noatime,nodiratime,ssd,subvol=@cache /dev/nvme0n1p2 /mnt/var/cache
mount -t btrfs -o noatime,nodiratime,ssd,subvol=@home /dev/nvme0n1p2 /mnt/home
mount /dev/nvme0n1p4 /mnt/backup
mount /dev/nvme0n1p5 /mnt/data
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
pacstrap /mnt base base-devel linux linux-firmware dhcpcd vim reflector git networkmanager sudo btrfs-progs openssh intel-ucode efibootmgr

genfstab -L /mnt >> /mnt/etc/fstab

arch-chroot /mnt
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc

vim /etc/locale.gen
# uncomment /etc/locale.gen
echo LANG=en_US.UTF-8 > /etc/locale.conf

locale-gen

vim /etc/hostname
# superyoko

passwd

useradd -m -G wheel yoko
passwd yoko

vim /etc/sudoers
# enable sudoer # uncomment wheel line

mkinitcpio -p linux


grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=grub
grub-mkconfig -o /boot/grub/grub.cfg
reboot

systemctl enable NetworkManager
systemctl start NetworkManager

nmcli dev wifi connect <> password <password>

git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
btrfs

sudo btrfs subvolume snapshot -r / /.snap001
=== snapshot 001

zsh
sudo pacman -S zsh zsh-autosuggestions zsh-syntax-highlighting zsh-completions
source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh

chsh -s /usr/bin/zsh

chown -R owner_name folder_name

sudo pacman -S adobe-source-han-sans-otc-fonts adobe-source-han-serif-otc-fonts noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra
sudo pacman -S  wqy-zenhei

sudo pacman -S gnome
sudo systemctl enable --now gdm

export https_proxy=http://xx.xx.
paru google-chrome
paru visual-studio-code
linuxqq

xray
v2raya
systemd 管理的 v2rayA#
使用 apt 等包管理器，或直接使用安装包进行安装的，一般都为这种方式。

新建文件夹 /etc/systemd/system/v2raya.service.d，然后新建一个 xray.conf 的文件，添加以下内容：

[Service]
Environment="V2RAYA_V2RAY_BIN=/usr/local/bin/xray"
注意检查 Xray 的路径是否正确。

重载服务：

sudo systemctl daemon-reload && sudo systemctl restart v2raya
sudo pacman -S fcitx5-im 
sudo pacman -S fcitx5-chinese-addons
/// === snap002 


sudo pacman -S mesa vulkan-intel intel-media-driver
pacman -S mesa xf86-video-intel vulkan-intel



====创建快照====
sudo pacman -S sof-firmware alsa-firmware alsa-ucm-conf alsa-utils # 声音固件
```

## desktop
sudo pacman -S gnome-browser-connector


