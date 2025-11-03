# Arch Linux Installation Guide 🧭

<p align="center">
<img src="https://archlinux.org/static/logos/archlinux-logo-light-90dpi.png" alt="Arch Linux Logo" width="500"/>
</p>

> [!NOTE]
> ONLY UEFI

- [Network Setup](#for-wifi)
- [Disk Partitioning](#-create-partitions)
- [Base Installation](#install-linux-packages-through-pacstrap)
- [Post-Install Setup](#after-coming-into-arch-login-by-your-username-and-password)
- [Bonus-Tools](#bonus-tools-which-will-help)
---

###  START  ###


#### For WiFi:
    iwctl
    device list
    station wlan scan
    station wlan0 get-networks
    station wlan0 connect "WIFINAME"
    station wlan0 show
#### For Ethernet:
    No need (Should auto connect)

### 🛰️ Network Check
```
ping google.com
```
> If not working
> ```
> rm /etc/resolv.conf
> echo "nameserver 1.1.1.1" > /etc/resolv.conf
>```

> [!TIP]
> If ping shows “temporary failure in name resolution”, it’s usually a DNS issue — fixing `/etc/resolv.conf` (as shown above) solves it.

#### 💽 Create Partitions
```
cfdisk
```
> Choose "GPT" from the table
> Make partitions -
> 1. Allocate atleast 300M for a partition (for boot).
> 2. Allocate atleast ~8G for second partition (for system).
> 3. Allocate atleast ~1G for third partition (for swap - optional but recommended).
> ALSO DO NOT FORGET to [WRITE] each one PARTITION BEFORE EXITING cfdisk


#### 🔍 Checking Partitions
```
lsblk
```
> You have to find the partition labels, For example -
> ```[root@archiso]# lsblk
> NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
> sda      8:0    0   8.3G  0 disk 
> ├─sda1   8:1    0   300M  0 part   <--- boot    (*)
> ├─sda2   8:2    0     1G  0 part   <--- swap    (%)
> └─sda3   8:3    0     7G  0 part   <--- system  (&)
>   ^^^^
>   └└└└── these are paritions label 
> ```


#### ⚙️ Make Ext4 File System
```
mkfs.ext4 /dev/sdX
``` 
> for the system (&)


#### Make Fat32 File System
```
mkfs.fat -F 32 /dev/sdX
```
> for the boot   (*)


#### Make Swap Partition
```
mkswap /dev/sdX
```
> for the swap   (%)


#### Check Paritions Label
```
blkid
```
> Just to confirm if partitions formatted correctly or not, 
> Find system as TYPE="ext4",
> Find boot as TYPE="vfat",
> Find swap as TYPE="swap".


#### Mount the Disk
```
mount /dev/sdX /mnt
```
> for system


#### Create Boot Directory
```
mkdir -p /mnt/boot/efi
```
> for boot


#### Mount boot partition
```
mount /dev/sdX /mnt/boot/efi
```
> mount boot partition to EFI dir


#### Enable Swap
```
swapon /dev/sdX
```


#### Checking Partitions
```
lsblk
```
> Check again, And it should look something like this-
> ```
> [root@archiso]
> NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
> sda      8:0    0   8.3G  0 disk 
> ├─sda1   8:1    0   300M  0 part /mnt/boot/efi   <--- nice!
> ├─sda2   8:2    0     1G  0 part [SWAP]          <--- nice!
> └─sda3   8:3    0     7G  0 part /mnt            <--- and nice!
> ```


#### Install Linux Packages Through Pacstrap
```
pacstrap /mnt base base-devel linux linux-firmware sudo networkmanager nano grub efibootmgr
```
> Installing essential packages in Arch Mount


#### Generate File System 
```
genfstab /mnt > /mnt/etc/fstab
```


#### Mount Arch Partition
```
arch-chroot /mnt
```
> [!TIP]
> Once you’re inside the chroot, you’re effectively using your installed Arch system — commands now affect your new OS, not the live ISO.

#### Set time & Date
```
ln -sf /usr/share/zoneinfo/<continent>/<region> /etc/localtime
```
> Example: `ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime`


#### Sync time & date
```
hwclock --systohc
```


#### Uncommenting Language Pack Name
```
nano /etc/locale.gen
```
> uncomment en_US.UTF-8 for English (Or Find your Region in It)


#### Set System Language
```
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```
> Same for here, Replace your Region with "en_US.UTF-8"


#### Refresh Locale
```
locale-gen
```


#### Entering Hostname in File
```
nano /etc/hostname
```
> name the system/computer


#### Setting Root Password
```
passwd
```


#### Adding User
```
useradd -m -G wheel -s /bin/bash <username>
```


#### Setting Password for User
```
passwd <username>
```


#### Configuring User Power for Sudo
```
EDITOR=nano visudo
```
> Uncomment the first coming line starting from "%wheel ALL=(ALL) NOPASSWD: ALL" and save, 
> This is for anyone part of wheel group can run sudo commands


#### Enabling NetworkManager
```
systemctl enable NetworkManager
```


#### Installing Grub Boot-Loader to System
```
grub-install /dev/sda
```


#### Making Grub Config File
```
grub-mkconfig -o /boot/grub/grub.cfg
```


#### Exiting From Arch Mount
```
exit
```
> to return to the live ISO environment


#### Unmounting the Drive
```
umount -R /mnt
```


#### Reboot
```
reboot
```

---


### After coming into Arch, Login by your Username and Password

```
sudo pacman -Syu
```
> For updating System and Packages

```
sudo pacman -S <desktop-environment> <display-manager>
```
> For GUI (Graphical User Interface)


### Some of the Popular desktop-enviroments and display-managers
- `xfce4 xfce4-goodies`              <-- lightweight with decent GUI
- `lightdm lightdm-gtk-greeter`      <-- recommended display manager for XFCE

- `lxqt lxqt-qtplugin`               <-- lighter than XFCE, Qt-based
- `lightdm lightdm-gtk-greeter`      <-- recommended DM (or sddm optional)

- `gnome`                            <-- full-featured, modern but heavy
- `gdm`                              <-- official GNOME display manager

- `plasma kde-applications`          <-- very customizable, modern, resource-heavy
- `sddm`                             <-- official KDE display manager

- `cinnamon`                         <-- good balance of features and usability
- `lightdm lightdm-gtk-greeter`      <-- works well with Cinnamon

---


### Install Some More Essential Packages
`sudo pacman -S <terminal> <webbrowser>`


### Some of the Popular Terminals

- `xfce4-terminal`         <-- default terminal for XFCE, lightweight and stable
- `lxterminal`             <-- default for LXDE/LXQt, very light
- `gnome-terminal`         <-- feature-rich, matches well with GNOME
- `konsole`                <-- KDE’s terminal, very customizable
- `xterm`                  <-- basic X terminal, ultra-lightweight
- `urxvt`                  <-- ultra-light and fast, customizable via .Xresources
- `alacritty`              <-- GPU-accelerated, very fast, modern (needs config)
- `kitty`                  <-- GPU-based, feature-rich with ligature support
- `tilix`                  <-- tiling terminal emulator with session saving
- `terminator`             <-- split-screen terminal with lots of features


### Some of the Popular Web Browsers

- `firefox`               <-- default on many distros, open-source and privacy-respecting
- `chromium`              <-- open-source base of Google Chrome, less proprietary
- `librewolf`             <-- Firefox fork with enhanced privacy defaults (AUR)
- `epiphany`              <-- GNOME Web browser, minimal and integrated
- `falkon`                <-- KDE browser, light and Qt-based

---


### DO NOT FORGET to enable the DISPLAY MANAGER after installing

- `sudo systemctl enable --now lightdm`       
> for LightDM
- `sudo systemctl enable --now gdm`
> for GDM
- `sudo systemctl enable --now sddm`
> for SDDM

---


### Bonus tools which will help
- `curl`                  <-- Download files or fetch web data
- `lsd`                   <-- Modern 'ls' command with icons and better formatting
- `ranger`                <-- Terminal file manager with VI-style navigation
- `btop`                  <-- Resource monitor (CPU, RAM, Disk, Network)
- `tldr`                  <-- Simplified man pages (shows practical examples)
- `clamav`                <-- Scan files and folders for viruses
- `buku`                  <-- Command-line bookmark manager
- `chafa`                 <-- Convert images into terminal graphics

---

### 🧑‍💻 Author
**LUCKYS1NGHH**  
Written with ❤️ for learners installing Arch manually.

#####  Finished  #####
