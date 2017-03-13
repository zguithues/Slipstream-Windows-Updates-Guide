# Slipstream-Windows-Updates-Guide
This guide is intended to help others figure out how to slipstream updates into a windows update iso or usb device


/PreventPending
prevents installing items that makeing a "pending.xml" file.  effectively these are updates that need to preform reboot actions, if they're included the image is larger than a dvd-r.

---
C:\wincd>dism /get-imageinfo /imagefile:c:\wincd\install.wim

Deployment Image Servicing and Management tool
Version: 6.3.9600.17031

Details for image : c:\wincd\install.wim

Index : 1
Name : Windows 8.1 Pro
Description : Windows 8.1 Pro
Size : 13,185,962,705 bytes

Index : 2
Name : Windows 8.1
Description : Windows 8.1
Size : 13,116,079,066 bytes

The operation completed successfully.

---

C:\wincd>dism /mount-wim /wimfile:c:\wincd\install.wim /mountdir:c:\wincd\dismmo
unt /index:1

Deployment Image Servicing and Management tool
Version: 6.3.9600.17031

Mounting image
[==========================100.0%==========================]
The operation completed successfully.

---

~1.5hrs

C:\wincd>dism /image:C:\wincd\dismmount /Add-Package /PackagePath:C:\wincd\wsuso
ffline\client\w63-x64\glb

Deployment Image Servicing and Management tool
Version: 6.3.9600.17031

Image Version: 6.3.9600.17031

Processing 1 of 184 - Adding package Package_for_KB2876331~31bf3856ad364e35~amd6
4~~6.3.1.0
[==========================100.0%==========================]
Processing 2 of 184 - Adding package Package_for_KB2894852~31bf3856ad364e35~amd6
4~~6.3.2.0
[===========================99.5%========================= ]
Processing 3 of 184 - Adding package Package_for_KB2894853~31bf3856ad364e35~amd6
4~~6.3.1.3
[==========================100.0%==========================]

....

Processing 182 of 184 - Adding package C:\wincd\wsusoffline\client\w63-x64\glb\W
indows8.1-KB3014442-x64.msu
[===========================99.8%========================= ]
Processing 183 of 184 - Adding package C:\wincd\wsusoffline\client\w63-x64\glb\W
indows8.1-KB3016437-x64.msu
[==========================100.0%==========================]
Processing 184 of 184 - Adding package C:\wincd\wsusoffline\client\w63-x64\glb\W
indows8.1-KB3172614-x64.msu
[===================        33.2%                          ]

Error: 0x800f081e

The specified package is not applicable to this image.
The specified package is not applicable to this image.
The specified package is not applicable to this image.
The specified package is not applicable to this image.
The specified package is not applicable to this image.
The specified package is not applicable to this image.
Package C:\wincd\wsusoffline\client\w63-x64\glb\Windows8.1-KB3172614-x64.msu may
 have failed due to pending updates to servicing components in the image. Try th
e command again.The command completed with errors.
For more information, refer to the log file.

The DISM log file can be found at C:\Windows\Logs\DISM\dism.log

C:\wincd>dism /image:C:\wincd\dismmount /Add-Package /PreventPending /PackagePat
h:C:\wincd\wsusoffline\client\w63-x64\glb\Windows8.1-KB3172614-x64.msu

Deployment Image Servicing and Management tool
Version: 6.3.9600.17031

Image Version: 6.3.9600.17031

Processing 1 of 1 - Adding package C:\wincd\wsusoffline\client\w63-x64\glb\Windo
ws8.1-KB3172614-x64.msu
[==========================100.0%========================= ]
The operation completed successfully.

---

~10min

C:\wincd>dism /image:c:\wincd\dismmount /cleanup-image /analyzecomponentstore

Deployment Image Servicing and Management tool
Version: 6.3.9600.17031

Image Version: 6.3.9600.17031

[==========================100.0%==========================]

Component Store (WinSxS) information:

Windows Explorer Reported Size of Component Store : 8.74 GB

Actual Size of Component Store : 8.34 GB

    Shared with Windows : 4.71 GB
    Backups and Disabled Features : 3.32 GB
    Cache and Temporary Data : 315.46 MB

Date of Last Cleanup : 2014-11-21 11:55:11

Number of Reclaimable Packages : 34
Component Store Cleanup Recommended : Yes

The operation completed successfully.

---

C:\wincd>dism /image:c:\wincd\dismmount /cleanup-image /startcomponentcleanup /r
esetbase

Deployment Image Servicing and Management tool
Version: 6.3.9600.17031

Image Version: 6.3.9600.17031



Error: 0x800f0806

The operation could not be completed due to pending operations.

The DISM log file can be found at C:\Windows\Logs\DISM\dism.log

#Unable to cleanup due to the "pending" updates.  if we include "/preventpending" we are able to shrink

---

~20 min

C:\wincd>dism /unmount-wim /mountdir:C:\wincd\dismmount /commit

Deployment Image Servicing and Management tool
Version: 6.3.9600.17031

Image File : c:\wincd\install.wim
Image Index : 1
Saving image
[==========================100.0%==========================]
Unmounting image
[==========================100.0%==========================]
The operation completed successfully.

---

6:09p

