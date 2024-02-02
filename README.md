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
pacstrap /mnt base base-devel linux linux-firmware dhcpcd vim reflector git networkmanager sudo btrfs-progs openssh

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

git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si

zsh
sudo pacman -S zsh zsh-autosuggestions zsh-syntax-highlighting zsh-completions
source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh

chsh -s /usr/bin/zsh

root btrfs subvolume snapshot / backupname

pacman -S hyprland
Hyprland

rofi
配置rofi

pacman -S mesa xf86-video-intel vulkan-intel

sudo pacman -S adobe-source-han-sans-otc-fonts adobe-source-han-serif-otc-fonts noto-fonts noto-fonts-cjk noto-fonts-emoji

sudo pacman -S gdm
systemctl enable gdm

// 备份hyprland配置

paru google-chrome
paru visual-studio-code
ssh-keygen -t RSA -C "xx"


linuxqq
xray
v2raya
配置内核

谷歌账号同步

====创建快照====
sudo pacman -S sof-firmware alsa-firmware alsa-ucm-conf alsa-utils # 声音固件
sudo pacman -S  wqy-zenhei
#sudo pacman -S adobe-source-han-serif-cn-fonts wqy-zenhei # 安装几个开源中文字体。一般装上文泉驿就能解决大多 wine 应用中文方块的问题
#sudo pacman -S noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra # 安装谷歌开源字体及表情


waybar
以及字体

sudo pacman -S wget
https://github.com/Alexays/Waybar/wiki/Examples

配置壁纸
hyprpaper
sudo pacman -S mako

+ alsa-utils brightnessctl
bind = , xf86audioraisevolume, exec, amixer set Master 5%+ # 调大音量
bind = , xf86audiolowervolume, exec, amixer set Master 5%- # 调小音量
bind = , xf86monbrightnessdown, exec, brightnessctl set 5%- # 调高亮度
bind = , xf86monbrightnessup, exec, brightnessctl set 5%+ # 调低亮度

sudo pacman -S intel-media-driver
驱动浏览器加速 https://xland.cyou/p/arch-linux-configuration-driver-and-software/

sudo pacman -S fctix5 fctix5-im fcitx5-chinese-addons

/etc/environment

GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
SDL_IM_MODULE=fcitx

exec-once=fcitx5 --replace -d

sudo pacman -S fctix5-im fcitx5-chinese-addons



第一次运行要取消勾选 Input Method页的 Only show current language,然后找到 Pin Yin ，双击添加

切换输入法默认为 ctrl+空格



~/.config/hyprland/hyprland.conf 添加 exec-once = fcitx5 -d



~/.bashrc 添加

export GTK_IM_MODULE=fcitx

export QT_IM_MODULE=fcitx

export XMODIFIERS=@im=fcitx



设置输入框的DPI: 

1. fcitx5-configtool->Addons -> Classic User Interface -> ✅ Use Per Screen DPI

    fcitx5-configtool->Addons -> Classic User Interface -> Force Font DPI on Wayland 144

发现 Google Chrome 也很模糊？你也可以修复它，只是用不同的方式。只需在新选项卡中打开 chrome://flags/#ozone-platform-hint，将其设置为“自动”，然后重新启动即可。

```


