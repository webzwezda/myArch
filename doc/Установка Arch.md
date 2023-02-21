# Установка Arch linux и BSPWM

Установка через UEFI и при помощи WiFi. На ноутбук Xiaomi 15 pro.

Name | Soft | Description
--|--|--
Оконный менеджер | [BSPWM](https://github.com/baskerville/bspwm) | Тайлинговый оконный менеджер


## Запись образа через Rufus

* [Rufus](https://rufus.ie/ru/) 
* [Arch Download](https://archlinux.org/download/)

Для версий Rufus ≥ 3.0 выберите **GPT в Схема раздела**. После нажатия СТАРТ вы получите диалоговое окно для выбора режима, выберите **Записать в режиме DD-образ**.

## Первый экран установки

После того как мы загрузились с флешки, следует подключиться к интернету. Для WiFi это утилита `iwctl`.

```bash
ip link                # покажет наш WiFi адаптер, у меня wlan0
iwctl                  # вызов утилиты
station wlan0 connect RT-5WiFi-0C6C           # подключение к WiFi роутеру
2817006250             # пароль
exit                   # Выход из программы 
ping -c 3 google.com   # Проверка соединения с сервером google.com
```

```
timedatectl set-ntp true   ## Синхронизация времени
timedatectl set-timezone Asia/Moscow    ## Выбор нашего региона
timedatectl status 
```  

## Разбивка диска

Есть два диска 

* /dev/sda
	- sda1 # UEFI
	- sda2 # /
* /dev/sdb
	- sdb1 # home
	
Диски разбиватся утилитой `fdisk`

command | descriptions
---|---
fdisk -l       | список всех дисков
fdisk /dev/sda | начать работу с диском
m              | help
g              | create new GPT table
n              | new part 
1  (part)      | +500M (uefi)
p              | view all part
t              | Изменить метку или присвоить
L              | Увидеть список разделов (q - выход из подсказки)
1              | uefi
w              | write
q              | exit

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

если что то пошло не так, загружаемся снова, подкидываем интернет и монтируем корень

```
mount /dev/sda2 /mnt
arch-chroot /mnt
```  

```
ln -sf /usr/share/zoneinfo/Asia/Vladivostok /etc/localtime  
ls /usr/share/zoneinfo/Asia  
hwclock --systohc  
pacman -S micro  
micro /etc/locale.gen
en_US.UTF-8 UTF-8
ru_RU.UTF-8 UTF-8
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
micro /etc/hostname (Сюда пишем имя компьютера) myArch
micro /etc/hosts  
127.0.0.1   localhost
::1         localhost
127.0.1.1   myArch.localdomain    myArch
```  
  
Users & Passwords

```
passwd 4114 
useradd -m webzwezda
passwd webzwezda 4114
usermod -aG wheel,audio,video,optical,storage webzwezda
userdbctl groups-of-user webzwezda
pacman -S grub efibootmgr micro sudo ntfs-3g dosfstools mtools 
EDITOR=micro visudo user ALL=(ALL) ALL
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
pacman -S iwd
exit
reboot
systemctl --now enable iwd
sudo micro /etc/pacman.conf
[multilib]  
include = /etc/pacman.d/mirrorlist
sudo reboot  
sudo pacman -Syyy
sudo pacman -S ttf-jetbrains-mono 
sudo shutdown now
```  

Keyboard

```
sudo micro /etc/X11/xorg.conf.d/00-keyboard.conf
```

```
Section "InputClass"  
    Identifier "system-keyboard"  
    MatchIsKeyboard "on"  
    Option "XkbLayout" "us,ru"  
    Option "XkbModel" "pc105"  
    Option "XkbOptions" "grp:alt_shift_toggle"  
EndSection 
```

## BSPWM INSTALL

```
sudo pacman -S xorg xorg-xinit mesa bspwm sxhkd alacritty numlockx nitrogen thunar unzip lxappearance scrot xclip

.xinitrc
exec sxhkd &
exec bspwm

mkdir -p .config/bspwm  
mkdir -p .config/sxhkd  

micro .config/bspwm/bspwmrc
#1 /bin/bash
sxhkd &

micro .config/sxhkd/sxhkdrc
super + {_,shift + }Return
	{alacritty, bspc node -s biggest.local}

chmod u+x .config/bspwm/bspwmrc
```

## NVIDIA

```
sudo pacman -Syu
sudo pacman -S xf86-video-intel nvidia nvidia-settings nvidia-utils
sudo reboot
```


## Soft

```
sudo pacman -S openssh lxappearance-gtk3
```

### YAY

```
yay nordic-theme tdrop
```


## Nord theme

[Nord Firefox](https://addons.mozilla.org/ru/firefox/addon/nord-theme/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search)
