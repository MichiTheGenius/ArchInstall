# Install arch linux

## Change your keyboard layout. For german it would be:
```shell
loadkeys de
```

---

## Enable wifi
> Enter the command `iwctl`
>
> Enter the command `device list` to see all available devices
>
> Enter `station NAMEOFDEVICE scan` to set your device to scan mode
>
> Enter `station NAMEOFDEVICE get-networks` to list all available networks
>
> Enter `station NAMEOFDEVICE connect SSID` to  connect to your SSID
>
> Enter the password when prompted and `exit` iwctl

---

## Set your clock to use ntp
```shell
timedatectl set-ntp true
```
---

## Partition your disk with `cfdisk`
> Enter the command `cfdisk /dev/name_of_drive`: name_of_drive being the drive you want to install Arch Linux on
>
> For UEFI boot choose the `gpt` disk label. For legacy boot use the `dos` disk label.
>
> make a boot partition with a size of 500M and set the bootable flag
>
> (optional) make a swap partition with the size of 2GB
>
> make a root partition with the rest of the size

---

## Format your partitions

### The **boot** partition

> for **UEFI**
>   
```shell
mkfs.fat -F32 /dev/name_of_boot_partition
```

> for **legacy**
>
```shell
mkfs.ext4 /dev/name_of_boot_partition
```

### The optional **swap** partition
```shell
mkswap /dev/name_of_swap_partition
```

### The **root** partition
```shell
mkfs.ext4 /dev/name_of_root_partition
```

---

## Mounting the partitions
```shell
mount /dev/name_of_root_partition /mnt
(optional) swapon /dev/NAMEOFSWAPPARTITION
```
---

## Installing the system
```shell
pacstrap /mnt base base-devel linux linux-firmware vim iwd
```

---

## Generating the filesystem table
> this is so that Arch knows where to mount the the partitions on every boot
```shell
genfstab -U /mnt >> /mnt/etc/fstab
```

---

## Chroot into the system
```shell
arch-chroot /mnt /bin/bash
```

---

## Set timezone
```shell
ln -sf /usr/share/zoneinfo/REGION/CITY /etc/localtime
```

---

## Set locale
```shell
vim /etc/locale.gen
```
> uncomment your locale and execute the command to generate your locale info

```shell
locale-gen
```

---

## set keyboard layout
```shell
localectl set-keymap LAYOUT
```
> after installing a desktop environment and X11 run this command to change the graphical keymap

```shell
localectl set-x11-keymap LAYOUT
```

---

## Set hostname
```shell
vim /etc/hostname
```
> enter a hostname and save the file

---

## Change the password of the root user
```shell
passwd
```

---

## Add a new normal user
```shell
useradd -m NAMEOFUSER
usermod -aG wheel NAMEOFUSER
passwd NAMEOFUSER
EDITOR=vim visudo
```
> Remove the comment from the line: **%wheel ALL=(ALL:ALL) ALL**
>
> This allows members of the wheel group to execute any command when using sudo

---

## Install the bootloader
> for UEFI
>

```shell
pacman -S efibootmgr dosfstools os-prober mtools grub
mkdir /boot/EFI
mount /dev/NAMEOFBOOTPARTITION /boot/EFI
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck
grub-mkconfig -o /boot/grub/grub.cfg
```

> for legacy
>

```shell
mount /dev/NAMEOFBOOTPARTITION /boot
grub-install /dev/NAMEOFDRIVE (NOT A PARTITION!)
grub-mkconfig -o /boot/grub/grub.cfg
```

---

## Finishing touches
> Install and enable networkmanager for internet access at fresh boot

```shell
pacman -S networkmanager
systemctl enable NetworkManager
```

> exit the chroot environment with the `exit` command
>
> unmount everything with `umount -R /mnt`
>
> reboot the system with `reboot` and enjoy!
