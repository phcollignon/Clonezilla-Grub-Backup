# clonebackup
Clone disk or partition to image files with Grub and Clonezilla

## Usage :
Easily clone your partition or disk to an image file for backup.   

1. Plug an external usb disk (exFat formated) 
2. Reboot
3. Choose a predefined backup config 
4. Wait and see ..  your computer boots a linux Clonezilla iso and backup is automatic done to image files

If you want to restore, boot the Clonezilla live entry and follow instructions to restore.

## Installation
Boot your standard linux 

Download Clonezilla iso (https://clonezilla.org/), copy to /isos  :

```
wget https://osdn.net/projects/clonezilla/downloads/71563/clonezilla-live-2.6.3-7-amd64.iso
sudo mkdir /isos
sudo cp clonezilla-live-2.6.3-7-amd64.iso /isos
``` 

Edit the Grub configuration file, there are two sample entry menu, 
you just have to set the target usb disk, the target path, and the source partition or disk for each entry.

```
git clone https://github.com/phcollignon/clonebackup.git
cd clonebackup
sudo cp 40_custom -rf /etc/grub.d/40_custom 
sudo vi /etc/grub.d/40_custom
``` 

For a partition backup :
```
menuentry "Backup Linux partition" {
        # define here the backup disk
        set backup_target=/dev/sdb1  
        # define here the backup path on the target backup disk
        set backup_target_path=backup_laptop_linux
        # define here the partition to backup
        set backup_source=sda1
        loopback loop $isofile
        linux (loop)/live/vmlinuz boot=live live-config noswap nolocales edd=on nomodeset keyboard-layouts=\"\" locales=\"\" vga=788 ip=frommedia nosplash toram=filesystem.squashfs  ocs_prerun=\"mount $backup_target /mnt \" ocs_prerun1=\"mkdir -p /mnt/$backup_target_path/$backup_source \" ocs_prerun2=\"mount --bind /mnt/$backup_target_path/$backup_source /home/partimag/\" ocs_live_run=\"ocs-sr -q2 --batch  -j2 -z1p -sc -p reboot saveparts autoname $backup_source \" ocs_live_extra_param=\"\" ocs_live_batch=\"yes\" findiso=$isofile
        initrd (loop)/live/initrd.img
}

```

For a disk backup : 

```
menuentry "Backup Windows disk" {
        # define here the backup disk
        set backup_target=/dev/sdb1
        # define here the backup path on the target backup disk
        set backup_target_path=backup_laptop_windows_disk
         # define here the disk to backup
        set backup_source=sda
        loopback loop $isofile
        linux (loop)/live/vmlinuz boot=live live-config noswap nolocales edd=on nomodeset keyboard-layouts=\"\" locales=\"\" vga=788 ip=frommedia nosplash toram=filesystem.squashfs  ocs_prerun=\"mount $backup_target /mnt \" ocs_prerun1=\"mkdir -p /mnt/$backup_target_path/$backup_source \" ocs_prerun2=\"mount --bind /mnt/$backup_target_path/$backup_source /home/partimag/\" ocs_live_run=\"ocs-sr -q2 --batch  -j2 -z1p -sc -p reboot savedisk autoname $backup_source \" ocs_live_extra_param=\"\" ocs_live_batch=\"yes\" findiso=$isofile
        initrd (loop)/live/initrd.img
}

```

Update Grub and reboot
```
sudo update-grub2 
sudo reboot
```


