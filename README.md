# myArch

```
ip link  
iwctl
station wlan0 connect RT-5WiFi-0C6C
2817006250 
exit  
ping -c 3 google.com
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



```
pacstrap /mnt base linux linux-firmware  
pacman -Sy archlinux-keyring (если пакеты не ставятся)  
genfstab -U /mnt \>\> /mnt/etc/fstab  
vim /mnt/etc/fstab  
arch-chroot /mnt (Меняем корневой каталог)
```









Users & Passwords

```
passwd 4114 
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

  
  


```
sudo pacman -S xorg xorg-xinit mesa bspwm sxhkd alacritty numlockx nitrogen thunar unzip lxappearance scrot  
```

```

