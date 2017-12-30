---
layout: post
title:  "Fix slow wireless transfer speed between PC and android device"
date:   2017-12-30 11:24:00 +1100
categories: android
---
Recently I needed to copy some big (> 10 Gb) files from PC to my android phone. Surely these files could have been copied pretty fast using USB connection but not from the couch while watching my favorite TV show. 

In the past I was able to achieve wireless transfer speeds between 3Mbs and 5Mbs. But this time I was getting only around 1Mbs. It might have been due to the new phone or the new router. My setup at the time was: 

* PC running Windows 10
* Shared folder
* Android 7 phone
* [ES File Explorer/Manager PRO](https://play.google.com/store/apps/details?id=com.estrongs.android.pop.pro&hl=en) connecting to shared folder via SMB.

After spending considerable amount of time researching the problem and trying different solutions I came up with an alternative approach - FTP server.
I had two choices, either [built-in FTP server](https://www.windowscentral.com/how-set-and-manage-ftp-server-windows-10) or [FileZilla](https://www.howtogeek.com/140352/how-to-host-an-ftp-server-on-windows-with-filezilla/). I chose FileZilla just because it seemed to be easier to setup. Built-in FTP server might produce better results. Nevertheless now my transfer speeds are between 10Mbs and 15Mbs. 

If you have similar problem I can recommend switching to FTP as it's not only faster but also makes creation of shared folders unnecessary.

