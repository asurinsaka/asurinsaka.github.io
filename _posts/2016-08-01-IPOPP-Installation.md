---
layout: post
title: IPOPP 2.5 installation
tags: [ipopp]
---

[IPOPP (the International Polar Orbiter Processing Package)](https://directreadout.sci.gsfc.nasa.gov/?id=dspContent&cid=68)
is a free software used to process raw hdf satellite data from Suomi National
 Polar orbiting Partnership(SNPP), Aqua, and Terra missions. It generate tiff
 and png files contains sst, chlor_a, crefl_rgb files form those raw data.


## How to install IPOPP
<!--break-->
To install ipopp, you can follow the following steps:

1. Create user ipopp.
2. Download ipopp from nasa.
 1. You need to register an account with nasa
 2. Download downloader_ipopp_2.5.sh
 3. Run it on your linux machine, make sure you have enough space.
3. Uncompress DRL-IPOPP_2.5.tar.gz to ~/IPOPP of ipopp user.
4. Run system_test.sh in ~/IPOPP, make sure all tests are passed except the
    database test. IPOPP 2.5 includes standalone mariadb and do not required
    pre-installed database.
5. Run install_ipopp.sh

##  Detailed Procedures of IPOPP Installation

To learn how ipopp works, I started with understanding ipopp installations. Here
is a picture of the ipopp installation procedures started after you fire the
script install_ipopp.sh

[![IPOPP Install](/iamges/ipopp_installation.png)](/iamges/ipopp_installation.pdf)
