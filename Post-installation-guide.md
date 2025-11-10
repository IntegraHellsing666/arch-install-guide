# Useful commands
If you wanna use your terminal a bunch, here are some of the most useful commands:
- `nano [file]` let's you edit a file.
- `cat [file]` let's you see the contents of a file.
- `cd [path]` changes your directory, for example `cd Desktop/` navigates to your desktop (to go back a directory use `../`).
- `ls [directory]` lists the files and directories at a given location, for example `ls` by itself lists files in the current directory.
- `tree [directory]` displays a tree structure of the files and folders recursively from the mentioned target (can also just use it just as `tree` for the current directory you're on), though you might have to download it with `sudo pacman -S tree` to be able to use it.
- `touch [name.format]` creates a file with the specified name and format, like `touch example.txt`.
- `rm [file]` will delete a file or, to delete a folder, use `rm -rf [directory]`
- `cp [file] [target]` will copy a file (or multiple) into the specified location, like `cp example.txt example2.txt ../Desktop/` would copy the two text files into Desktop from a location like Downloads.
- `mv [file] [target]` will move a file (or multiple) to the specified location, like `mv example.txt examplefolder/` would move the text file into a folder inside the current directory.

# Package management
## Downloading packages
Now that you have Arch and a desktop environment, you might be tempted to download things off of official websites like you're used to on Windows.
It's time to change that, welcome pacman, your package manager, to your life. At first it might feel weird to download apps off of a terminal, though once you start doing so and realise how quick and simple it is, you won't want to come back.

To download packages, you can search for them at the [official Arch repository](https://archlinux.org/packages/). Almost everything you could want is on there and you with how all packages there are routinely checked, it's your absolute safest go-to for downloads.
Now that you've spotted a few packages you want from there, let's say...Krita, my favourite drawing application, all you need to do is go to your terminal and run `sudo pacman -S krita`. You will be prompted for your user's password and will be shown the dependencies alongside the installation you want. To download more than one thing at the time, simply leave a space between package names, like `sudo pacman -S krita kwrite` (kwrite is a nice equivalent to a Windows notepad).

If a package isn't in the official repository, you can check the official website of the app you want and check for an x64 .tar file (tar is an archive similar to a zip). About zips...download `zip` and `unzip`, or an archive manager like `7zip` or `ark` from the official repository so you can extract and compress things easier, now back to the guide. To 

Downloading from tars can be a little inconvenient, even if it is also a very safe option since you get these from the official source. Another quite safe option is to download a flatpak. For this, either go to your `Discover` app if you downloaded KDE or to [Flathub](https://flathub.org/en) and find the package you wish. For `Discover`, simply download the package from there (make sure it's verified as a little extra measure of security). For Flathub, first `sudo pacman -S flatpak` and download the package with `flatpak install [name]`. The main issue with flatpaks is that, despite a bit of security from them being sandboxed, that bonus is also it's weakness because packages can have performance drops in comparison to a direct download.

Another solid option is to go to the [Arch User Repository](https://aur.archlinux.org/) (AUR). With the help of yay, a tool to help download from the AUR, it makes managing packages similar to the pacman route and you can even update these simultaneously instead of memorising which packages you got from where. The issue with the AUR is that, though rare, since the packages are just user uploaded, they _can_ contain malware.
To mitigate this, always check the __upstream URL__ and __PKGBUILD__. Additionally, check for the reputation of the package and latest comments.
For downloading from the AUR, I'd recommend first downloading yay by running `git clone https://aur.archlinux.org/yay-bin.git` (make sure you have git and base-devel downloaded by running `sudo pacman -S git base-devel`), changing directory with `cd yay-bin` and building with `makepkg -si`. Now that you have yay, you can download packages from the AUR the same way you would with pacman by using `sudo yay -S [name]`, but again, always do security checks before downloading from there.

## Updating packages
- To update packages from the official repository, use `sudo pacman -Sy [name]`, or better yet, do a full system update with `sudo pacman -Syu`
- For tars, download a new one.
- For flatpaks, either go to `Discover` and update there or run `flatpak update`.
- For AUR, if you have yay, just `sudo yay -Sy [name]` or again, better yet, `sudo yay -Syu` (check the packages before updating since PKGBUILDs can be editted).

## Deleting packages
- To delete official packages I recommend using `sudo pacman -Runs [name]` since this also removes all dependencies __that aren't used elsewhere__.
- For tars, depends on what you downloaded, but tends to be a bit messy.
- For flatpaks, uninstall in `Discover` or run `flatpak uninstall [name]`.
- For AUR, same as official, `sudo yay -Runs [name]`.

> As a note, if you want to check if you already have a package on your system, you can use `pacman -Q [name]`.

# Recommendations for a great system
- Get a firewall like `firewalld` from the official repository, enable it with `sudo systemctl enable firewalld` and restart your system (or start it with `sudo systemctl start firewalld`).
- Get `filelight` from the official repository for better visualising disk space usage.
- Get `gwenview` from the official repository for viewing images and videos (or `vlc` for videos if you prefer).
- Get `calibre` for E-book management.
- Get `libreoffice` for an opensource substitute of things like Microsoft word, excel and powerpoint.
- Get `timeshift` to make a backup of your system in case anything ever goes wrong.
- Get `man-db` and `man-pages` to check documentation on your terminal with `man [package you want to learn about]`.
- Get `plocate` or `kfind` to find files or directories in your system.
- Get `elisa` to listen to music.

> Just go to the official repository and search for things you want like a specific app name or a keyword to find available options.
> Another good source of packages is the KDE apps (all are in the official repo as well) by going to https://apps.kde.org/.