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
 - type the following command to select the right disk (I'm assuming you have a new disk or you've taken a Complete Backup of All your data)
   ```
   sel dis <x>
   ```
   [where <x> is typically the number of the disk you want to use - such as 0, 1, 2 etc.]
 - type the below command to clear all partitions
   ```
   clean
   ```
   
  ### Creating partitions
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

### Format Partitions and Assign Drive Letters

Now format all the disks as follows...

#### EFI Boot Partition
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

#### Win tools Partition
- select the win tools partition...
  ```
  sel par 2
  ```
- format partition....
  ```
  for fs=ntfs quick
  ```

#### The Main Windows Partition
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

#### win recovery partition
- select the windows recovery  partition...
  ```
  sel par 4
  ```
- format partition....
  ```
  for fs=ntfs quick
  ```

#### windows Users partition
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
  [where `<drvltr>` is the drive letter where the iso containing the install image is mounted.]

- to Start the windows install process...
  ```
  dism /apply-image /imagefile:<drvltr>:\sources\install.wim  /index:6  /applydir:c:
  ```
  [here index 6 represents win 11 pro. you can change the index (refer to Previous step for more info) to install a different version of win 11]
  
it takes some time to copy the win 11 files to `C:\` drive... Be patient... After copying files is complete - you should see a Success message.

- to copy boot files to the efi partition...
  ```
  bcdboot c:\windows /s B: /f all
  ```
with that base install of win 11 is complete. You can close powershell and close the default installer - which should reboot the system.

## OOBE / Network configuration

once the system reboots - you're taken through the Out Of Box Experience (OOBE) setup wizard...

- just before it asks to connect to network - press `Shift + f10` to open powershell again and type,,,
  ```
  oobe\bypassNRO
  ```
the system will reboot - and resume the OOBE setup...

- now press `Shift + f10` again to open powershell and type,,,
  ```
  ipconfig /release
  ```
this disconnects you from network. Post this point - you have to proceed with `Limitted win 11 setup`...

- when asked to connect to network - select the option `I have no internet` 

- when asked to create a Microsoft Account - select the option `create a Local User account` 

proceed with the rest of the OOBE setup as usual.

At the end - you should have a custom - but stock install of Windows 11 pro...


## Moving Users folder to a different Partition...

there are ways to move Content (like Documents, Music, Pictures, Videos etc.) to different drives. But windows mostly does a messy job of this. You're more likely to end up with multiple home directories for your user - which is a really bad idea. Worse still - there's no option in the win 11 installer to move the Users folder to a different drive / partition (during the installation itself).

So, This must be done after the installation. You'll need couple of files to do this - which can be found in the `move Users` folder.

Follow these steps to move the `Users` folder to a different drive / partition...
 - ensure that the Target Drive is well formatted and has a Drive Letter
   [be careful not to use the `win tools` or the `win recovery` partition drive letters here.]
 - copy both files in the `move Users` folder of this repo - and place both files in the root / parent folder of the Target drive
 - now edit the xml file - and change the Drive letter in the file (just before `\Users`) - to match the drive letter of the Target drive
 - next run the bat file as Administrator - to trigger the move process.
   [in the background - it runs the sysprep tool - which initiates another run through the OOBE setup process (but this time in Unattended mode). After the Users folder is moved to the Target drive - the OOBE setup may ask you to create a new Admin user
 - when prompted to create a new user - create a new user as usual
 - now you should have the Users folder in your new Target drive... :)


