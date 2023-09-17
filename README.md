
# dotfiles
a repo containing dot config files / other config files for an overall better linux experience...

My NixOS config files for System recovery
------------------------------------------------

It is possible to restore the entire system to its former glory - just by using the NixOS Configuration file...

Keep the most up-to-date version of the 'configuration.nix' file in your github / gitlab repo...

Whenever there's a problem with your system - you can download the config file from your repo - and do a fresh install (as per instructions given below)... 98% of your previous system can be restored by using this method...

Instructions for Usage
------------------------

these config files can be used only in NixOS (not with any other distro)

  - boot from the NixOS live iso - but do not run the installer (if you have your NixOS config file)
    
  - download the configuration.nix file from your online repo - and save it in your home dir

  - mount your disk partitions and format them as required... I did the following...
    ```
    # I use cfdisk / cgdisk to partition the disk as needed...
    # use mkfs to format the newly created partitions
    
    mount /dev/sdb12 /mnt
    mkdir /mnt/boot
    mkdir /mnt/home
    mount /dev/sdb1 /mnt/boot
    mount /dev/sdb13 /mnt/home
    ```
  - autogenerate NixOS config files - in the next step, we will replace the configuration.nix file with the one we have in our home dir... For now, generate the config files as follows...
   ```
   nixos-generate-config --root /mnt
   ```
  - now copy the configuration.nix file in your home dir - to its Recognized place in the system...
   ```
   cp ./configuration.nix /mnt/etc/nixos/
   ```
  - lastly, run the installation...
   ```
   nixos-install
   ```
[if your config files are well written - the install should proceed smoothly.]

  - at the end of the install - we need to set the root passwd
  
  - after the install - reboot the system and do the following...
     - set passwds for users created during the install process]
     - do some minor tweaks / configurations as needed
  
  - the system should now be ready to use...  
