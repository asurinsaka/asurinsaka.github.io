---
layout: post
title: NMR software and file formats
tags: [nmr, ucsf, sparky, poky, vnmrj, ]
---

Recently worked on helping my previous PI analyze some NMR (Nuclear Magnetic Resonance) data for some molecules. I am used to analyze NMR data with [Sparky](https://www.cgl.ucsf.edu/home/sparky/). I am going to discuss about some NMR related software and file formats in this post.<!--break-->


# Short background

The raw data are usually obtained from Bruker NMR machines or Varian NMR machines. Varian was trying to produce 1 GHz NMR machine and failed. It went bankrupt and was bought by Agilent. The software from Bruker is called [TopSpin](https://www.bruker.com/en/products-and-solutions/mr/nmr-software/topspin.html). TopSpin is free for acedamic usage. The software from Varian is called Vnmrj. It was not free but now there is an open source version called [OpenVnmrj](https://openvnmrj.org/).


# UCSF format

`ucsf` format is the format used by Sparky and its successor Poky. 

## Conversion from bruker

The ucsf format could easily be converted from bruker format by using the following command:

```
bruk2ucsf.exe .\USF_BH_20230328_TOCSY\27\pdata\1\2rr usf_bh_20230328_tocsy.ucsf
```

`bruk2ucsf.exe` comes with sparky software distribution.

The raw date from Bruker will only have ser file. We have to use TopSpin to process the raw data to get the 2rr file. 

## Conversion from vnmrj

To get ucsf format from Vnmrj is a little more complicated. You have to have Vnmrj software installed in your system. The current OpenVnmrj can be installed on Ubuntu 20.04. After opening your data in OpenVnmrj, you have to run `trace='f1'` and `flush` in Vnmrj. Then run:

```
vnmr2ucsf exp7/procpar exp7/datdir/phasefile exp7.ucsf H H
```
to generate the ucsf file. 
The exp7 means you have opened your date in experiment7 in your Vnmrj softawre. 
The exp7 folder should be in `~/vnmrsys/data` folder. 


## UCSF data format

Detailed information about the data format could be found [here](https://www.cgl.ucsf.edu/home/sparky/manual/files.html).

Let's analyze the headers of a C13HSQC file:

```
00000000   55 43 53 46  20 4E 4D 52  00 00 02 01  00 02 ** **  ** ** ** 00  00 00 00 4D  6F 6E 20 41  UCSF NMR......*****....Mon A
0000001C   70 72 20 30  33 20 31 31  3A 33 32 3A  33 34 20 32  30 32 33 00  00 00 00 00  00 00 00 00  pr 03 11:32:34 2023.........
00000038   00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  ............................
00000054   00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  ............................
00000070   00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 40 01 B4  00 00 00 00  .....................@......
0000008C   00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  ............................
000000A8   00 00 00 00  00 00 00 00  00 00 00 00  31 33 43 00  00 00 00 00  00 00 04 00  00 00 04 00  ............13C.............
000000C4   00 00 00 80  42 FB 8B 64  46 A2 20 D8  42 96 03 2B  00 00 00 00  00 00 00 00  00 00 00 00  ....B..dF. .B..+............
000000E0   80 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  ............................
000000FC   00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  ............................
00000118   00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  ............................
00000134   31 48 00 00  00 00 00 00  00 00 04 00  00 00 04 00  00 00 00 80  43 FA 13 85  45 DF 36 DB  1H..................C...E.6.
00000150   40 A0 00 40  00 00 00 00  00 00 00 00  00 00 00 00  80 00 00 00  00 00 00 00  00 00 00 00  @..@........................
0000016C   00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  ............................
00000188   00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  ............................
000001A4   00 00 00 00  00 00 00 00  00 00 00 00  00 00 00 00  C6 39 F0 00  C7 22 5F 00  C7 74 0B 00  .................9..."_..t..
```

| Position          | hex           | meaning            |
|-------------------|---------------|--------------------|
|00-09              | 55 43 53 46  20 4E 4D 52  00 00 | UCSF NMR|
|10-13              | 02 01  00 02                    | 2 dimetion, real data, 2 for current format|
|180(B4)-185        | 31 33 43 00  00 00              | 13C (Carbon 13) |
|188-191            | 00 00 04 00                     | 1024 data points |
|196-199            | 00 00 00 80                     | tile size 128    |
|200-203            | 42 FB 8B 64                       | 125 MHz       |
|204-207            | 46 A2 20 D8                       | Spectrum width 20752.4219 Hz  |
|208-211            | 42 96 03 2B                       | Center of spectrum 75 ppm |
|308(134)-313       | 31 48 00 00  00 00            |1H (proton)    |
|316-319            | 00 00 04 00                   | 1024 data  points |
|324-327            | 00 00 00 80                     | tile size 128    |
|328-331            | 43 FA 13 85                   | 500 MHz       |
|332-335            | 45 DF 36 DB                   | Spectrum width 7142.857 Hz |
|336-339            | 40 A0 00 40                   | Center of Spectrum 5 ppm|

The header for carbon 13 starts at 180.
The header for proton starts at 180 + 128 = 308. 
The data starts at 180 + 128 + 128 = 436.






