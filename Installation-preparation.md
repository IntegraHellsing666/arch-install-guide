> This section will cover both Virtual Machine usage and a full flash drive installation.

## Getting the ISO
Go to https://archlinux.org/download/ and download the image using either torrent, or scroll down and pick a mirror to download from from your country or close-by for a faster download.
There are other options listed for downloading the image, though these are the two most used.

## Verifying the ISO
If you're already on a Linux system, do any of the steps below to verify the downloaded image:
- Verify the BLAKE2b by running `b2sum -c [arch ISO]` and comparing the hash with the one pasted on the downloads page under the section __Checksums and signatures__.
- Verify the SHA256 by running `sha256sum -c [arch ISO]` and comparing with the hash pasted the same place as stated above.
- Verify using PGP fingerprint by running `gpg --auto-key-locate clear,wkd -v --locate-external-key pierre@archlinux.org` to download the signature and `gpg --verify [arch ISO sig] [arch ISO]`.

If you're on a Windows system, I recommend just dropping the ISO image into https://emn178.github.io/online-tools/sha256_checksum.html to check the SHA256 and/or https://emn178.github.io/online-tools/blake2b/file/ to check the BLAKE2b. From there, copy paste the output into a notepad and on a line below copy paste the same signature(s) from the __Checksums and signatures__ section of the download page, this makes it easier to compare the hashes.

## Virtual machine
Now that you have your (hopefully not corrupted) ISO, if you want to just play around with Arch instead of fully committing to it, get a virtual machine manager like [VirtualBox](https://www.virtualbox.org/) or similar.
Create a new virtual machine and for ISO selection just input the ISO you downloaded. VirtualBox is very intuitive to use, though feel free to check a guide for your first attempt.
Once the virtual machine is setup, you can refer to the [main installation](./README.md).

## Flash drive full installation
If you're fully committing to Arch, I recommend first getting one or two flash drives.
One for a backup of your current system, so choose whichever disk size you find suitable for that, the other for the installation medium itself, I recommend going for at least 32GBs even if you don't need that much in practice (can always use it as a general flash drive later).

#### Etching
Now that you have your installation flash drive ready, get an ISO image writer like [Balena Etcher](https://etcher.balena.io/) or [Rufus](https://rufus.ie/en/), I personally recommend Balena for it's simplicity (Rufus has given me some issues before with a couple installations).
Plug in your installation drive and etch the ISO on it (leave the ISO file in your computer with the target drive being the plugged in flash drive, don't manually move the ISO to the flash drive).

#### Booting into the installation medium
With your installation medium ready, reboot your computer and go into your BIOS (check how to do that for your motherboard brand, usually by spamming `delete` or an `F-key` as your computer boots up).
Make sure UEFI is enabled in your BIOS settings, and enable it if it isn't already (this step changes depending on motherboard brand too, though you can explore the BIOS settings to find UEFI).
Go to your boot menu and change the order so that the first thing to boot is your installation medium.
Save and exit.
If you are thrown into the arch installation screen, then congratulations you can now move onto the [main installation](./README.md).