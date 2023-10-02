# dotfiles
a repo containing dot config files for various Tiling Window Managers - in order to make them more Accessible.....

-----------------------
# Perfect Windows 11 install

The default windows 11 install is really messy. It has the following limitations...
 - it does disk partitions on its own - you have very little control
 - it puts everything in one place
 - it forces you to connect to a Network
 - It forces you to have / use a Microsoft Account
 - it has lots of Bloatware (garbage)

 A better way to install windows 11 is detailed here below......

 -------------------------------
 ## Preparation

 Follow these steps...
  - download official Win 11 iso from Microsoft (verify the iso with the sha256 sum hash)
  - put it in a Ventoy disk or flash it to a pen drive
  - boot from the pen drive

## Partitioning

Once the iso boots - we get the default installer. Don't click Next or Install. Instead, do the following
 - press `Shift + f10` to bring up powershell
 - enter diskpart by typing `diskpart` and press the `<enter>` key
 - type `sel dis <x>` to select the right disk (I'm assuming you have a new disk or you've taken a Complete Backup of All your data)
 - type `clean` to clear all partitions
 - create an efi boot partition as follows...
   ```
   crea par efi size=100
   ```
- create a primary win tools partition as follows...
   ```
   crea par pri size=200
   ```
- create a main Windows partition as follows...
   ```
   crea par pri size=250000
   ```
- create a Windows recovery partition as follows...
   ```
   crea par pri size=250
   ```
- create an Windows Users partition as follows...
   ```
   crea par pri size=100000
   ```
you can create more partitions on the free space available - as per your wish.

Now format all the disks as follows...

- select efi partition...
  ```
  sel par 1
  ```
- format partition....
  ```
  for fs=fat32 quick
  ```
- assign drive letter `b` to it...
  ```
  assign letter=b
  ```
- select the win tools partition...
  ```
  sel par 2
  ```
- format partition....
  ```
  for fs=ntfs quick
  ```
- select main windows partition...
  ```
  sel par 3
  ```
- format partition....
  ```
  for fs=ntfs quick
  ```
- assign drive letter `c` to it...
  ```
  assign letter=c
  ```
- select the windows recovery  partition...
  ```
  sel par 4
  ```
- format partition....
  ```
  for fs=ntfs quick
  ```
- select windows users partition...
  ```
  sel par 5
  ```
- format partition....
  ```
  for fs=ntfs quick
  ```
- assign drive letter `d` to it...
  ```
  assign letter=d
  ```
partition all other drives as per your requirements.

to exit diskpart - type `exit` and press the `<enter>` key.

## Main install

For the main install - follow these steps...

- to get index details for the windows install image...
  ```
  dism /get-imageinfo /imagefile:<drvltr>:\sources\install.wim
  ```
  [where <drvltr> is the drive letter where the iso containing the install image is mounted.]

- to Start the windows install process...
  ```
  dism /apply-image /imagefile:<drvltr>:\sources\install.wim  /index:6  /applydir:c:
  ```
  [here index 6 represents win 11 pro. you can change the index to install a different version of win 11]
  
it takes some time to copy the win 11 files to `C:\` drive... Be patient... After copying files is complete - you should see a Success message.

- to copy boot files to the efi partition...
  ```
  bcdboot c:\windows /s B: /f all
  ```
with that base install of win 11 is complete. You can close powershell and close the default installer - which should reboot the system.

## OOBE / Network config skip

once the system reboots - you're taken through the OOBE wizard...

- just before it asks to connect to network - press `Shift + f10` to open powershell again and type,,,
  ```
  oobe\bypassNRO
  ```
the system will reboot - and resume the OOBE setup...

- now press `Shift + f10` again to open powershell and type,,,
  ```
  ipconfig /release
  ```
this disconnects you from network. Post this point - you have to proceed with limitted win 11 setup...

- when asked to connect to network - select the option `I have no internet` 

- when asked to create a Microsoft Account - select the option `create a Local account` 

proceed with the rest of the OOBE setup as usual.

At the end - you should have a custom - but stock install of Windows 11 pro...

  
