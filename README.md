# Slipstream-Windows-Updates-Guide
This guide is intended to help others figure out how to slipstream updates into a windows update iso or usb device

## Needed:
* dism (Deployment Image Servicing and Management tool)
** https://technet.microsoft.com/en-us/library/hh825236.aspx
* imgburn (http://www.imgburn.com/)
* Rufus (https://rufus.akeo.ie/)
* Windows DVD iso (https://gist.github.com/mkuba50/27c909501cbc2a4f169be4b4075a66ff)
* wsusoffline (http://www.wsusoffline.net/)

## Getting Ready
First you need to extract the windows iso.  To make things easy, I recommend making a folder at the root of your c drive, i used `c:\wincd\`

Here are the path's I used, yours may be different...
`c:\wincd\dismmount` <- folder that dism mounts the install.wim into
`c:\wincd\wsusoffline` <- location of wsusoffline
`c:\wincd\Win 8.1 disks` <- location of windows iso's
`C:\wincd\Win 8.1 disks\Win8.1_English_x64` <- location of extracted windows disks

Once your iso has been extracted, move the `sources\install.wim` file to `c:\wincd\`

> You may wish to rename this file `install-orig.wim` and make a copy to work with.

now, open a command prompt as ADMINISTRATOR

type `dism`

you should see:
```
C:\wincd>dism

Deployment Image Servicing and Management tool
Version: 6.3.9600.17031

```

Followed by many details about how to use it.  If you don't see this, then you need to install dism.

## Explore the install.wim

type the following: `dism /get-imageinfo /imagefile:c:\wincd\install.wim`
You should see something like this:
```
C:\wincd>dism /get-imageinfo /imagefile:c:\wincd\install.wim

Deployment Image Servicing and Management tool Version: 6.3.9600.17031

Details for image : c:\wincd\install.wim

Index : 1 Name : Windows 8.1 Pro Description : Windows 8.1 Pro Size : 13,185,962,705 bytes

Index : 2 Name : Windows 8.1 Description : Windows 8.1 Size : 13,116,079,066 bytes

The operation completed successfully.
```

As you can see, my iso includes 2 indexes; Win 8.1 Pro & Win 8.1.  I don't actually need to have the Win 8.1 included, so i'm going to extract the first index from install.wim into install1.wim.
`dism /export-image /sourceimagefile:c:\wincd\install.wim /destinationimagefile:c:\wincd\install1.wim /sourceindex:1`

```
C:\wincd>dism /export-image /sourceimagefile:c:\wincd\install.wim /destinationimagefile:c:\wincd\install1.wim /sourceindex:1

Deployment Image Servicing and Management tool
Version: 6.3.9600.17031

Exporting image
[==========================100.0%==========================]
The operation completed successfully.
```

## Now we will mount the install1.wim

`dism /mount-wim /wimfile:c:\wincd\install1.wim /mountdir:c:\wincd\dismmount /index:1`

```
C:\wincd>dism /mount-wim /wimfile:c:\wincd\install1.wim /mountdir:c:\wincd\dismmount /index:1

Deployment Image Servicing and Management tool Version: 6.3.9600.17031

Mounting image [==========================100.0%==========================] The operation completed successfully.
```

## Install Updates
We now have the install1.wim file mounted to c:\wincd\dismmount.  Now we just need to install the updates to the mounted folder.

You can use wsusoffline to download all available updates, or you can pre-select a few.  I HIGHLY recommend adding kb3021910 which fixes windows update.

Single Package: `dism /image:C:\wincd\dismmount /Add-Package /PackagePath:C:\wincd\updates\windows8.1-kb3021910-x64_e291c0c339586e79c36ebfc0211678df91656c3d.msu`
Folder of Packages: `dism /image:C:\wincd\dismmount /Add-Package /PackagePath:C:\wincd\wsuso ffline\client\w63-x64\glb`

## Commit changes back to the install1.wim
`dism /image:c:\wincd\dismmount /cleanup-image /analyzecomponentstore`

## Final Steps 
Now copy/move the install1.wim file back into the extracted windows disk.  make sure to rename it to `install.wim`.

Then use Imgburn to recreate the iso.  make sure to make it bootable and use the same bootsector as the original disk.

You may also want to include this in a `EI.CFG` file, also in sources.
```
[EditionID]
Professional

[Channel]
Volume

[VL]
1
```
This prevents windows from requiring a license key to install.
