# Installation preparation
Refer to [the pre-installation steps](./Installation-preparation.md).

# Installation

> For readability use `setfont -d` and whenever the screen gets cluttered either `ctrl+l` or  type `clear`.
> For changing keyboard layout (default is en_us), use `loadkeys [whatever]` , like `loadkeys de-latin1` for a typical german keyboard. Other layouts can be listed with `localectl list-keymaps`.

## Check boot mode
Use `cat /sys/firmware/efi/fw_platform_size`. If it returns `64` then you're booted with UEFI, perfect, specifically a 64bit version, even more perfect. If it returns `32` you're booted on UEFI too, so can still proceed, and though it's a 32bit version, shouldn't affect much. If it returns `No such file or directory` you're not booted on UEFI and instead on BIOS or CSM, if that happens, refer to your motherboard specifications or just `reboot`, go into the BIOS, and try searching for UEFI to turn it on, otherwise the rest of the installation won't work.

## Internet connection

> Use `ping ping.archlinux.org` or any other website to check for internet connection, if it sends a few bytes you can skip this chapter and just hit `ctrl+c` to cancel the command once it's running.

Preferably you have an ethernet cable plugged in, if not, then use `iwctl` for connection to wifi:
1. `iwctl`
2. `device list`
3. `station [name] scan`, whichever device you found (usually `wlan0`, though if that isn't there then check for any non-loopback ones)
4. `station [name] get-networks`
5. `station [name] connect [SSID]`, the ssid is what you'd usually see as the wifi name on a phone
6. fill in password of network
7. `exit`
8. ping a domain of your choosing again to confirm connection

## Update system clock
Should already be synced, though to make sure just run `timedatectl`

## Partitioning
There are wayyy too many partitioning schemes so this section is entirely up to preference, in which case please just refer to https://wiki.archlinux.org/title/Partitioning for more options, specially section __2.3 Example Layouts__.
Here I'll just show a common one with three partitions, similar to what you'd get using Windows.

1. Use `cfdisk`
2. (If asked for label type) select __gpt__
3. Delete all already existing partitions (use arrow keys to navigate and enter to confirm)
4. To make a boot partition, select `free space`, `new`, and give it `1G`
5. To make a swap partition, again `free space`, `new`, and give it at least 4GBs, though I'd recommend some 16 or more GBs `16G`
6. To make the root partition, same step, but leave the size blank so it uses up all remaining space by default
7. `write` and type `yes` to confirm
8. `quit`

To see the partitions, use `lsblk`.

#### Partition formatting
> Assuming the partitions made are under /dev/sda when listed with `lsblk`, specifically, with boot under sda1, swap under sda2, and root under sda3. Adapt if under sdb or another name.

1. For formatting the root partition as ext4, use `mkfs.ext4 /dev/sda3`
2. For formatting the boot partition as fat32, use `mkfs.fat -F 32 /dev/sda1`
3. For formatting the swap partition, use `mkswap /dev/sda2`

Again use `lsblk`, just to make sure everything is fine, even if the output will look the same as before.

## Mounting
1. To mount the root partition use `mount /dev/sda3 /mnt`
2. To mount the boot partition, first make the directory it will be mounted to by using `mkdir -p /mnt/boot/efi`, then to actually mount use `mount /dev/sda1 /mnt/boot/efi`
3. To mount the swap partition, use `swapon /dev/sda2`

Again run `lsblk`, this time it should show where everything is mounted, make sure it's correct with sda1 mounted to /mnt/boot/efi, sda2 showing `[SWAP]` and sda3 mounted to /mnt.

## Package installation
This will download all the packages you want to the actual system instead of the installation medium. Though the only required are `base`, `linux` and `linux-firmware`, that would be awful to work with. I'll give an example of what I consider to be actual essentials for a working environment, you can add more though that's not needed since you can always install them later.

Use `pacstrap -K /mnt base linux linux-firmware sof-firmware base-devel grub efibootmgr nano networkmanager bash-completion`
Here: 
- `sof-firmware` helps with sound cards
- `base-devel` let's you use the AUR
- `grub` is the boot loader
- `efibootmgr` is for UEFI support for grub
- `nano` is a text editor you'll need to edit files from the terminal the rest of the installation (and honestly even after)
- `networkmanager` the name speaks for itself
- `bash-completion` let's you press tab to complete the name of directories or files in commands

> You can also download the microcode for your CPU here, helps with security. For that, you'd want `amd-ucode` for AMD or `intel-ucode` for Intel, self explanatory.
> You can also get easier access to documentation by getting `man-db` and `man-pages`, so then if you have any questions about a tool, you can use `man [tool name]` to get the wiki page on your terminal.

## Fstab
Use `genfstab -U /mnt >> /mnt/etc/fstab`.
Confirm the contents by using `cat /mnt/etc/fstab`.

## Chroot
To boot into the live environment, use `arch-chroot /mnt`.

## Time
#### Timezone
To set a timezone, use `ln -sf /usr/share/zoneinfo/[region]/[city] /etc/localtime`
This is where bash-completion comes in handy. If you wanted, for example, Berlin as your zone, you could, after zoneinfo, type Eur and then press tab to fill in, then Ber tab again and congrats you've found your zone quicker. If you can't find your zone, you can use `ls` on the region or city part to list the available options, for example, `ls /usr/share/zoneinfo/` or `ls /usr/share/zoneinfo/Europe/`.

#### Hardware clock
Run `hwclock --systohc`.
To prevent drift over time, you can sync it with `timedatectl set-ntp true`.

## Locales
Use `nano /etc/locale.gen` and uncomment (remove the `#` from) the locale you want to use, for example `en_US.UTF-8 UTF-8` for the standard american or `de_DE.UTF-8 UTF-8` for the standard german. To exit and save, use `ctrl+x` to exit, type `y` to save, `enter` to confirm. 

> You can also uncomment multiple locales, or, better yet, just go with the standard american for now and edit them later in your desktop environment for ease.

Once you have the locale(s) you want, run `locale-gen`.

Use `nano /etc/locale.conf` and set your `LANG`, for example `LANG=en_US.UTF-8` or `LANG=de_DE.UTF-8`.

Lastly, if you want a different keyboard by default instead of using `loadkeys` every time, then `nano /etc/vconsole.conf` and write, for example, `KEYMAP=de-latin1` for a german keyboard.

## Hostname
This gives your system an identifiable name on networks, it's not a name for a user, don't worry, that comes later.
Run `nano /etc/hostname` and name your system whatever you wish (though can't have capitalisation or start with a dash, old UNIX things, things like this will show up once in a blue moon, specially with user names).

## Root password
Run `passwd` to give your root a password (you won't see it as you type, though it'll ask you to fill it in a second time to confirm).
Please, for my mental sanity, make this password the best you can and don't use the same one for your user. The times you log in as root will be rare after making users so feel free to write it down somewhere if it helps memorise, just make sure it's good because access to your root means your _entire_ system is gone.

## Users
To add a user, run `useradd -m -G wheel -s /bin/bash [name]`. Just like with the hostname, don't capitalise this (pretty sure it blocks you from doing so nowadays, so you'd need to go out of your way to do it, but still, I went out of my way and ended up locked out of my system for a couple days since some programs really don't like capitalised user names).

To give your user a password, run `passwd [name]`. And again, make this different to the root password but just as good since this will be a user with sudo privileges. About that...

#### Adding user to sudoers
Run `EDITOR=nano visudo`, scroll to the bottom until you see `Uncomment to allow members of group wheel to execute any command`, the line below that is what you need to uncomment so that it only says `%wheel ALL=(ALL) ALL`.

## Other adjustments before rebooting
- To enable your network manager, run `systemctl enable NetworkManager`.
- If you have an SSD, do it a favour and run `systemctl enable fstrim.timer`.
- To have your mirrorlist managed for you, `sudo pacman -S reflector` and `systemctl enable reflector.timer`
- To have access to lots of official packages you'll want, like Steam, turn on multilib by running `nano /etc/pacman.conf`, scrolling down until you see `multilib` and uncommenting both the line that says `[multilib]` and the line below `Include = /etc/pacman.d/mirrorlist`. Save and then run sudo pacman -Sy for pacman to update the change.

Lastly, enable grub. For this, first run `grub-install /dev/sda`, then `grub-mkconfig -o /boot/grub/grub.cfg`. 

## Reboot
Exit the live environment by just typing `exit`, you know you've succeeded if `root` shows coloured. 
As a redundancy before rebooting, run `umount -R /mnt` to unmount manually, letting you see if anything got scuffed.
Lastly, `reboot` and, once off, remove the flash drive to ensure you get thrown in your new arch installation.

## Next steps for a nice working computer
1. On the new installation, login as either root or your user.
2. Make sure you have internet access by pinging a domain like at the start.
3. Make sure your keyboard layout is the one you selected, if not then use `loadkeys` and then change it on the desktop environment you will soon have.
4. Use `setfont -d` for better visibility if you wish.
5. Download your desired desktop environment or window manager, for example KDE or hyprland (here I will show KDE), and SDDM, by running `sudo pacman -S plasma sddm`. Just press enter for the default installation options.
6. Download other packages you want, for example, by running `sudo pacman -S konsole kate firefox discord obsidian vlc gwenview dolphin`, anything really, just search for stuff you might want at https://archlinux.org/packages/ and type their names there. A terminal emulator like `konsole` or `kitty` are pretty much mandatory, the rest you can also just download later.
7. Enable SDDM by running `systemctl enable sddm` and lastly `sudo systemctl enable --now sddm`.

> If you wish to use GNOME, you will need to install `gnome` and `gdm` instead of plasma and sddm, respectively. For more information on getting it setup, go to https://wiki.archlinux.org/title/GNOME.

With that, you should now be logging in to your desktop environment, enjoy.

> To update your system packages, run `sudo pacman -Syu` whenever you want.
> Also, because I know you want steam, download it after your graphics drivers (next section) with `sudo pacman -S steam`

## Drivers
If you have an AMD graphics card, congratulations your guide ended on the previous section.
If you poor soul have a NVIDIA graphics card like me, don't worry, the driver installation process is much easier nowadays, though will still take a little research.

Hopefully you already know your GPU model, but if you don't, then you can check it the extra nice way by downloading hyfetch and fastfetch with `sudo pacman -S hyfetch fastfetch`. Then `hyfetch -c`, select the options you want and after saving the configuration run `hyfetch`. Now you can both flex your new arch installation and know your GPU.

Check https://wiki.archlinux.org/title/NVIDIA, specifically, the table at the beginning with the GPU families and driver options. To find out your GPU's family go to https://nouveau.freedesktop.org/CodeNames.html and `ctrl+f` the model you have. From there, check where that lands on the arch wiki table and note the drivers associated with it. ___In theory___, for modern cards, a solid option would be downloading nvidia-open (nvidia is no longer supported), nvidia-utils and nvidia-settings with the command `sudo pacman -S nvidia-open nvidia-utils nvidia-settings` and rebooting.

Because NVIDIA still kinda relies on Xorg as a display manager and KDE by default uses wayland, you might first want to install compatibility with `sudo pacman -S xorg-xwayland`.

___I would, in reality, recommend___ going through these steps instead:
1. `reboot`.
2. Instead of logging in as a user, use `ctrl+alt+F6` to go into a tty.
3. Login as your user in the tty.
4. `sudo pacman -S xorg-xwayland nvidia-open nvidia-utils nvidia-settings`
5. `reboot`.

If, after logging in to your desktop environment again, the cursor moves smoothly instead of like a powerpoint slideshow, then your drivers are functioning properly.

Here is where you can now confidently download steam by running `sudo pacman -S steam` and going with the options that best align with your GPU instead of just defaults.

Now go enjoy your arch linux, and be sure to tell everyone you know that you use arch at every opportunity.

For other general information for getting used to arch, go to the [post-installation guide](Post-installation-guide.md).
