# Fresh Install Arch Linux Guide
Fresh installation of Arch Linux for beginner

After twice Arch installation in a couple days, I decide to write down an installation step in case I need it in next week.

## Ingredients:
```sh
1. Live Boot Arch Linux
2. MBR Partition
3. GRUB boot loader
4. NetworkManager
5. Xorg display server
6. LightDM display manager
7. KDE Desktop Environment
8. Plasma Desktop Session
9. WIFI ofc
```
## Instructions :
First of all. I just gonna use the last ingredient.
### 1. Connect to Internet 
Run the following command
```sh
# rfkill unblock wifi
```
Idk why my wireless lan is on rfkill list. Check to make sure wifi is unblocked
```sh
# rfkill list
```
see Soft Blocked under Wireless Lan should be 'no' and u good to go.
```sh
# wifi-menu
```
This command will run dialog menu. Select your SSID and type your password.
```sh
# ping google.com
```
Check if connection soft and tasty

### 2. Make Partition
This one is sweaty bc i have win10 and I dont want to format it by accident.
```sh
# cfdisk
```
Use this command. It's really handy. Or if you have more than one this you should list it first.
```sh
# fdisk -l
```
Now find the correct one and run the previous command like this
```sh
# cfdisk /dev/sdX1
```
X for letter of device and 1 is number of partition. Replace it with the right one. Again, becareful.

cfdisk

#### After cfdisk opened select on free space and new.

- Type partition size. please spare 1 to 2 times of ram size if you planning to make swap partition.
- Primary or Extended I guess Linux dont really care. But remember that MBR only support up to 4 primary partition.
- Dont forget to flag it as bootable.
- Repeat step 1 to 3 for swap partition. Dont flag it as bootable but change the type insted to "Linux swap / Solaris". Remember the size is between 1-2 times of Ram size. But if you are rich and can afford 16GB RAM ++ maybe you can go to next step.
- select "Write" and enter.
- Type "yes" to confirm.

Format the Partition.
```sh
# mkfs.ext4 /dev/sdX1
```
And the Swap Partition.
```sh
# mkswap /dev/sdX2
```
Dont forget to mount and activate swap.
```sh
# mount /dev/sdX1 /mnt
# swapon /dev/sdX2
```
At my first installation i forgot to activate swapon and it shown error everytime i boot. :(

### 3. Install Linux
Finally the cooking part.

Edit /etc/pacman.d/mirrorlist using nano.
```sh
# nano /etc/pacman.d/mirrorlist
```
Add the closest server near you to the top of the list. I Pasted it from ```https://www.archlinux.org/mirrorlist/all/``` ngl
```sh
Server = https://mirror.telkomuniversity.ac.id/archlinux/$repo/os/$arch
Server = http://mirror.labkom.id/archlinux/$repo/os/$arch
Server = http://suro.ubaya.ac.id/archlinux/$repo/os/$arch
```
Ctrl+X and Enter.

From here you can install all pacakage that you need for your os using ```pacstrap /mnt *package*```. Or you can do it later after entering ```/mnt``` using ```pacman -S *package*```.
```sh
# pacstrap /mnt base linux base-devel
```
Bake in preheated oven, for 10 minutes or until juices run clear.

### 4. Configure System

- Generate fstab file.
```sh
# genfstab /mnt >> /mnt/etc/fstab
```
Taste it in case u put too much sugar.
```sh
# cat /mnt/etc/fstab
```
If no error shown, you are good.

- Chroot
```sh
# arch-chroot /mnt
```
With this you are changing root and entering your new system. The bash would look deferent```[root@archiso /]#```

- Set the Timezone
```sh
# ln -sf /usr/share/zoneinfo/Asia/Jakarta /etc/localtime
```
Here you can find yours using ```ls```
```
ls /usr/share/zoneinfo
```
generate ```/etc/adjtime```
```sh
# hwclock --systohc --utc
```

- Localization
```sh
# nano /etc/locale.gen
```
Find ```en_US.UTF-8 UTF-8``` and delete # to uncomment it.
```sh
# locale-gen
```
Now create file ```/etc/locale.conf```
```sh
# nano /etc/locale.conf
```
Type ```LANG=en_US.UTF-8```, hit ctrl+X and Enter.
|/etc/locale.conf
|--
|LANG=en_US.UTF-8

Set the keyboard layout
```sh
# nano /etc/vconsole.conf
```
|/etc/vconsole.conf
|--
|KEYMAP=de-latin1

or you can keep it default.

- Network configuration
Create hostname file with following command. and type your hostname.
```sh
# nano /etc/hostname
```
|/etc/hostname
|--
|studioh

Replace studioh with anything you want.

Add matching entries to hosts
```sh
# nano /etc/hosts
```
|/etc/hosts
|--
|127.0.0.1	localhost<br/>::1		  localhost<br/>127.0.1.1	studioh.localdomain	studioh

Again you can replace studioh with anything you want.

- Root and User access
```sh
# passwd
```
Type the password you want.<br> At this point i also create user for me.
```sh
#useradd -m -g users -G wheel,storage,power -s /bin/bash sunu
```
You can replace sunu with your user name. And dont forget to set password for that user
```sh
# passwd sunu
```
Install sudo so u can access root as user
```sh
# pacman -S sudo
```
Edit sudoers file and uncomment ```%wheel ALL=(ALL) ALL```
```sh
# nano /etc/sudoers
```
|/etc/sudoers
|--
|%wheel ALL=(ALL) ALL

to save the file, hit ctrl+x, type y and Enter.

- Install Network Manager
```sh
# pacman -S networkmanager
```
And enable service 
```sh
# systemctl enable NetworkManager
```
### 5. Enable Microcode
```sh
# pacman -S intel-ucode
```
or
```sh
# pacman -S amd-ucode
```
I used intell but if you want to install both i dont mind.
```sh
CONFIG_BLK_DEV_INITRD=Y
CONFIG_MICROCODE=y
CONFIG_MICROCODE_INTEL=Y
CONFIG_MICROCODE_AMD=y
```

### 6. Boot Loader
Your system is good and tasty. Next step is to install boot loader, here is the step.<br> Oh wait, i remembered that i have Win10 installed in another partition so i also need ```os-prober``` to detect another os and ```ntfs-3g``` to detect ntfs.
```sh
# pacman -S grub os-prober ntfs-3g
```
Install GRUB on disk. Yeah, i said on disk so u dont need to specify the partition number. Just disk letter.
```sh
# grub-install --target=i386-pc /dev/sdX
```
Sometimes it need to remount after install ```ntfs-3g``` to detect ntfs. But u can do it later Im just gonna put it here.
```sh
# os-prober
```
The result will identify Win10.<br>... or not. Depend on if you already remount.
```sh
# grub-mkconfig -o /boot/grub/grub.cfg
```
Here what you should run everytime you make change on GRUB. For example if you decide to add another os, you run ```os-prober``` and then ```grub-mkconfig -o /boot/grub/grub.cfg```. Get it?

## Cake Decoration
Your Arch Linux is good and fresh from the oven. But becareful its still hot. Just prepare the decoration while its colling down.

### 1. Display Server
Install Xorg
```sh
# pacman -S xorg xorg-server
```
### 2. Desktop Environment
I choose plasma as my desktop environment. you can choose any desktop environment that familiar like GNOME. Chocholate or vanilla all good.
```sh
# pacman -S plasma-meta kde-applications 
```
I add ```kde-applications``` that contain full set of KDE appliction.

### 3. Display Manager
```sh
# pacman -S lightdm lightdm-gtk-greeter
```
Enable lightdm at startup 
```sh
# systemctl enable lightdm
```
You already installed ```lightdm-gtk-greeter``` now load it from ```/etc/lightdm/lightdm.conf```
```sh
#nano /etc/lightdm/lightdm.conf
```
Find ```greeter-session``` and fill with ```lightdm-gtk-greeter```. Uncomment and save.

|/etc/lightdm/lightdm.conf
|--
|greeter-session=lightdm-gtk-greeter

See here if you want to experience with lightdm greeter https://wiki.archlinux.org/index.php/LightDM and edit ```lightdm.conf```.<br> For Example:

|/etc/lightdm/lightdm.conf
|--
|greeter-session= = lightdm-webkit2-greeter

## Serve The Cake
The next step is...<br>
Wait, there is no more step. That's mean your Arch Linux is ready to serve.<br>
Press Ctrl+d and type
```sh
# umount -R /mnt
```
Then you can reboot
```sh
# reboot
```
Voila. Finding the perfect vanilla cake recipe requires a celebration!
