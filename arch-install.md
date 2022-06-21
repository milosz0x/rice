pacman -Syy reflector
reflector -c Poland -a 6 --sort rate --save /etc/pacman.d/mirrorlist
pacman -Syyy
512MB sda1 EFI, REST sda2 ext4 (root)
mkfs.fat -F32 /dev/sda1
cryptsetup -y -v luksFormat /dev/sda2
cryptsetup open /dev/sda2 cryptroot
mkfs.ext4 /dev/mapper/cryptroot
mount /dev/mapper/cryptroot /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
pacstrap /mnt base base-devel linux-lts linux-firmware-lts linux-headers neovim git wget curl amd-ucode
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
ln -sf /usr/share/zoneinfo/Europe/Warsaw /etc/localtime
hwclock --systohc
nvim /etc/locale.gen # enUs
locale-gen
echo LANG=en_US.UTF-8 >> /etc/locale.conf
echo KEYMAP=pl >> /etc/vconsole.conf
nvim /etc/hostname
nvim /etc/hosts
pacman -S grub efibootmgr networkmanager mtools dosfstools reflector cups alsa alsa-utils alsa-firmware
nvim /etc/mkinitcpio.conf # ADD "keyboard keymap encrypt"
mkinitcpio -p linux-lts
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
