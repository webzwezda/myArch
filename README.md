# myArch

```
ip link  
iwctl
station wlan0 connect RT-5WiFi-0C6C
exit  
ping google.com
```

```
timedatectl set-ntp true  
timedatectl set-timezone Europe/Moscow   
timedatectl status
```

## Разбивка диска

Разделы на маленьком диске sda

\* 1 - UEFI
\* 2 - /

И один раздел на большом /sdb диске под /home

```
fdisk -l  
fdisk /dev/sda  
m - help  
g - create new gpt table  
n - new part  
1 - +500M (uefi)  
p - view all part  
нужно изменить тип разделов, поставить метки  
t - Изменить метку или присвоить  
L - Увидеть список разделов (q - выход из подсказки)  
1 - uefi  
19 - swap  
  
w - write   
q - exit
```

Форматирование разделов

```
mkfs.fat -F32 /dev/sda1 - UEFI  
mkfs.ext4 /dev/sda2     - /  
mkfs.ext4 /dev/sdb1     - /home
```

Монтирование разделов

```
mount /dev/sda2 /mnt  
mkdir /mnt/boot  
mkdir /mnt/home  
ls -l /mnt  
mount /dev/sda1 /mnt/boot  
mount /dev/sdb1 /mnt/home
```

Установка базовых пакетов

```
pacstrap /mnt base linux linux-firmware  
pacman -Sy archlinux-keyring (если пакеты не ставятся)  
genfstab -U /mnt \>\> /mnt/etc/fstab  
vim /mnt/etc/fstab  
arch-chroot /mnt (Меняем корневой каталог)
```

```
ln -sf /usr/share/zoneinfo/Europe/Moscow /etc/localtime  
ls /usr/share/zoneinfo  
hwclock --systohc  
pacman -S vim    
vim /etc/locale.gen  
en_US.UTF-8 UTF-8  
ru_RU.UTF-8 UTF-8  
locale-gen  
echo "LANG=en_US.UTF-8" \> /etc/locale.conf  
vim /etc/hostname (Сюда пишем имя компьютера) myArch  
vim /etc/hosts  
127.0.0.1   localhost  
::1         localhost  
127.0.1.1   myArch.localdomain    myArch
```

Users & Passwords

```
passwd 4111  
useradd -m webzwezda  
passwd webzwezda 4114  
usermod -aG wheel,audio,video,optical,storage webzwezda  
userdbctl groups-of-user webzwezda  
pacman -S sudo  
EDITOR=vim  
visudo  
%wheel ALL=(ALL) ALL
```

Grub

```
pacman -S grub  
pacman -S efibootmgr dosfstools os-prober mtools  
mkdir /boot/EFI  
mount /dev/sda1 /boot/EFI  
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck  
grub-mkconfig -o /boot/grub/grub.cfg
```

интернет

```
pacman -S iwd dhcpcd
exit  
reboot  
systemctl --now enable iwd  
sudo vim /etc/pacman.conf  
multilib]  
include = /etc/pacman.d/mirrorlist  
sudo reboot  
sudo pacman -Syyy  
  
sudo pacman -S ttf-jetbrains-mono  
sudo shutdown now
```

https://wiki.archlinux.org/title/Iwd_(%D0%A0%D1%83%D1%81%D1%81%D0%BA%D0%B8%D0%B9)  
  
https://notabug.org/owl410/owl_dotfiles/src/master/bspwm/bspwm_tokio_night  

https://www.youtube.com/watch?v=i9M94y8PIsU

