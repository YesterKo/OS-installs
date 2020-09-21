##Sources

Arch wiki
https://unix.stackexchange.com/questions/316846/unable-to-find-root-device
https://gist.github.com/YesterKo/840d9a7df3235ad4f30505ed87964134

##Formating
<details>
<summary>fdisk -l output</summary>

```
root@archiso # fdisk -l
Disk /dev/sda: 223.57 GiB, 240057409536 bytes, 468862128 sectors
Disk model: KINGSTON SUV400S
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: 6907FC2A-14AE-4C3B-8F7D-F98090460479

Device         Start       End   Sectors  Size Type
/dev/sda1       2048    999423    997376  487M EFI System
/dev/sda2     999424  59592703  58593280 27.9G Linux filesystem
/dev/sda3   59592704 264226815 204634112 97.6G Linux filesystem
/dev/sda4  264226816 468860927 204634112 97.6G Microsoft basic data


Disk /dev/sdb: 1.36 TiB, 1500301910016 bytes, 2930277168 sectors
Disk model: ST31500341AS    
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 0297B0BD-0484-476E-BA14-3449517A8E27

Device          Start        End    Sectors   Size Type
/dev/sdb1        2048   31250431   31248384  14.9G Linux swap
/dev/sdb2    31250432   62500863   31250432  14.9G Linux filesystem
/dev/sdb3    62500864 2305101823 2242600960     1T Linux filesystem
/dev/sdb4  2305101824 2930276351  625174528 298.1G Microsoft basic data


Disk /dev/sdc: 931.51 GiB, 1000204886016 bytes, 1953525168 sectors
Disk model: WDC WD10EZEX-00K
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: 51E65E5E-8007-419F-92F0-3FE15B5D855D

Device     Start        End    Sectors   Size Type
/dev/sdc1   2048 1953523711 1953521664 931.5G Linux filesystem


Disk /dev/sdd: 14.61 GiB, 15682240512 bytes, 30629376 sectors
Disk model:  SanDisk 3.2Gen1
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: A0A7D884-A74B-49E6-AA0C-32CAB58A6F08

Device     Start      End  Sectors  Size Type
/dev/sdd1   2048 30629342 30627295 14.6G Microsoft basic data


Disk /dev/loop0: 556.19 MiB, 583208960 bytes, 1139080 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

* /dev/sda1 - EFI partition
* /dev/sda2 - /
* /dev/sda3 - SSD partition
* /dev/sda4 - Windows partition

* /dev/sdb1 - Swap
* /dev/sdb2 - /mnt
* /dev/sdb3 - /home
* /dev/sdb4 - Windows partition

* /dev/sdc1 - /something

</details>

```
mount /dev/sda2 /mnt
mkdir /mnt/efi
mkdir /mnt/home
mkdir /mnt/tmp
mount /dev/sda1 /mnt/efi
mount /dev/sdb3 /mnt/home
mount /dev/sdb2 /mnt/tmp
swapon /dev/sdb1

reflector
pacstrap /mnt base linux linux-firmware
genfstab -U /mnt >> /mnt/etc/fstab

arch-chroot /mnt
```

## Setting time and date
* Set time zone

      ln -sf /usr/share/zoneinfo/Europe/Tallinn /etc/localtime

* Set RTC from system

      hwclock -w
      
* install neovim

      pacman -S neovim
      
* Uncomment `en_US.UTF-8 UTF-8` and needed locales in `/etc/locale.gen`

      nvim /etc/locale.gen
      
      en_US.UTF-8 UTF-8
      en_GB.UTF-8 UTF-8
      et_EE.UTF-8 UTF-8
      
* generate locales with:
      
      locale-gen
      
* Set the language

      echo "LANG=en_GB.UTF-8" > /etc/locale.conf
      
      
* Set the keyboard layout if you changed it

      nvim /etc/vconsole.conf
      
      KEYMAP=your-layout
      
## Network configuration

* Set the computer name

      echo Kodune-5-Arch > /etc/hostname

* Set matching name to the hosts file

      nvim /etc/hosts
      
      127.0.0.1     localhost
      ::1           localhost
      127.0.1.1     Kodune-5-Arch
      
* Set root password

      passwd
      
## Installing rEFInd

*  Install packages

      pacman -S refind intel-ucode
      refind-install
      
* Modify `refind_linux.conf`

      nvim  /boot/refind_linux.conf
<details>
<summary>Output of `nvim /boot/refind_linux.conf`</summary>

```
"Boot with standard options"  "archisobasedir=arch archisolabel=ARCH_202009"
"Boot to single-user mode"    "archisobasedir=arch archisolabel=ARCH_202009 single"
"Boot with minimal options"   "ro root=UUID=e6a2d31f-4510-4603-8c19-552c30613edd"
```

* Take the UUID and put it onto the other options (More about it [here](https://wiki.archlinux.org/index.php/REFInd#refind_linux.conf) and [here](https://unix.stackexchange.com/a/319842))

```
"Boot using default options"     "root=UUID=e6a2d31f-4510-4603-8c19-552c30613edd rw add_efi_memmap initrd=boot\intel-ucode.img initrd=boot\initramfs-linux.img"
"Boot using fallback initramfs"  "root=UUID=e6a2d31f-4510-4603-8c19-552c30613edd rw add_efi_memmap initrd=boot\intel-ucode.img initrd=boot\initramfs-linux-fallback.img"
"Boot to terminal"               "root=UUID=e6a2d31f-4510-4603-8c19-552c30613edd rw add_efi_memmap initrd=boot\intel-ucode.img initrd=boot\initramfs-linux.img systemd.unit=multi-user.target"
"Boot with minimal options"      "ro root=UUID=e6a2d31f-4510-4603-8c19-552c30613edd"

</details>

* reboot

