# Fresh Install Arch Linux Guide
Fresh installation of Arch Linux for beginner

After twice Arch installation in a couple days, I decide to write down an installation step in case I need it in next week.

## Ingredients:
```sh
1. Live Boot Arch Linux
2. MBR Partition
3. GRUB boot loader
4. netctl
5. Xorg display server
6. LightDM display manager
6. KDE Desktop Environment
7. Plasma Desktop Session
8. WIFI ofc
```
## Directions :
First of all. I just gonna use the last ingredient.
1. Connect to Internet 
Run the following command
```sh
#rfkill unblock wifi
```
Idk why my wireless lan is on rfkill list. Check to make sure wifi is unblocked
```sh
#rfkill list
```
see Soft Blocked under Wireless Lan should be 'no' and u good to go.
```sh
#wifi-menu
```
This command will run dialog menu. Select your SSD and type your password.
```sh
#ping google.com
```
Check if connection soft and tasty

2. Make Partition
This one is sweaty bc i have win10 and I dont want to format it by accident.
```sh
#cfdisk
```
Use this command. It's really handy. Or if you have more than one this you should list it first.
```sh
#fdisk -l
```
Now find the correct one and run the previous command like this
```sh
#cfdisk /dev/sdX1
```
X for letter of device and 1 is number of partition. Replace it with the right one. Again, becareful.

cfdisk
```sh
-After cfdisk opened select on free space and new.
-Type partition size. please spare 1 to 2 times of ram size if you planning to make swap partition.
-Primary or Extended I guess Linux dont really care. But remember that MBR only support up to 4 primary partition.
-Dont forget to flag it as bootable.
-Repeat step 1 to 3 for swap partition. Dont flag it as bootable but change the type insted to "Linux swap / Solaris". Remember the size is between 1-2 times of Ram size. But if you are rich and can afford 16GB RAM ++ maybe you can go to next step
-select "Write" and enter.
-Type "yes" to confirm.
```
