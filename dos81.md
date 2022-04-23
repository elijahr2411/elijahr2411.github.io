---
back: '/dos9x'
---
# MS-DOS 8.1

MS-DOS 8.1 is the last (known) version of MS-DOS. It was not released as a standalone OS, rather bundled with Windows ME as the backend.
Ironically, though Windows ME is known for it's poor stability, MS-DOS 8.1 is probably the newest and most stable version there is.
The major thing about it for me though, is that it has native FAT32 and Long Filename support. For this reason I recommend using it in place of
MS-DOS 6.22 pretty much anywhere.

The hard thing, though, is how to install it. MS-DOS 8.1 was not meant to be installed to a hard disk so it is a bit tricky to get a boot floppy to install from. Luckily, I've done all the hard work for you, and have placed a boot floppy of MS-DOS 8.1 at the bottom of the page. You can either follow the instructions below on how to use that floppy to install to a disk, or make the floppy yourself using the second set of instructions.

## Installing from the Floppy

At the bottom of the page is the boot floppy, generated exactly using the method below. Download it, flash it to a floppy (or if you're using a VM don't), then put it into your computer/VM. Follow the below instructions:

1. Boot from the floppy
2. Use FDISK to create a primary partition. (I recommend the utility XFDISK, a graphical partition manager far superior to MSFDISK. [Link](https://www.mecronome.de/xfdisk/download.php){:target="_blank"})
3. Format your partition using `format C:`
4. Either:
  a) Run `sys C:` to install the minimal system (The builtin version is a stub that tells you to use the Windows ME control panel, so I've written and included a batch script with the same syntax)
  or, b) Copy the files manually:
  ```
  copy A:\IO.SYS C:\
  copy A:\MSDOS.SYS C:\
  copy A:\COMMAND.COM C:\
  ```
5. Your hard drive should now be bootable. Note that this is a **very** minimal install. You'll need to copy some programs. Some can be found on the boot floppy, the rest are located in a Windows ME install at `C:\Windows\Command`. I've included this folder as a floppy file at the bottom of the page. Flash it to a floppy or extract and put it on a CD-ROM (You'll need to first install CDROM drivers), put it into your PC/VM, and copy all it's contents to C:\DOS
6. You should also create an AUTOEXEC.BAT and CONFIG.SYS.

## Creating the floppy yourself
Here are the instructions for creating the floppy on your own, if you don't want to use mine. You'll need a copy of Windows ME ([WinWorld](https://winworldpc.com/download/17c2bbc5-92c2-abe2-809c-c38211c3a4ef){:target="_blank"} is my favorite website for early windows versions), as well as the Real-Mode DOS patch which allows MS-DOS 8.1 to boot standalone. The website where this was hosted is gone but it can be found [here](https://web.archive.org/web/20060218191214/http://www.dewassoc.com/support/winme/real_dos.htm){:target="_blank"} on the wayback machine.

1. Install Windows ME to a VirtualBox VM. This is fairly straightforward, but [here's a nice guide](https://www.sysnettechsolutions.com/en/install-windows-millennium-virtualbox-windows-10/){:target="_blank"} if you can't figure it out.
2. At the top of the VirtualBox window, click Devices>Floppy Drives>Choose/Create a floppy disk>Create. Create a 1.44MB floppy and uncheck the Format as FAT12 box. Mount the floppy.
3. From the Windows ME desktop, navigate to Start>Settings>Control Panel>Add or remove programs>Startup disk>Create disk. Follow the prompts and create the startup disk.
4. Close the window and open up the floppy in file explorer. We're now going to do some cleanup.
5. Open `config.sys` in notepad and either remove everything and write your own, or remove every section except for [CD] and [COMMON]. Remove the headings of these lines too, and remove the `DEVICEHIGH=RAMDRIVE.SYS /E 2048` line. Your file should now look like this:

```
device=oakcdrom.sys /D:mscd001
device=btdosm.sys
device=flashpt.sys
device=btcdrom.sys /D:mscd001
device=aspi2dos.sys
device=aspi8dos.sys
device=aspi4dos.sys
device=aspi8u2.sys
device=aspicd.sys /D:mscd001
files=10
buffers=10
dos=high,umb
stacks=9,256
lastdrive=z
```

6. Now open up `AUTOEXEC.BAT` in notepad. Blank the file completely. You can write your own autoexec.bat here. I recommend the following:

```
@echo off
PATH A:\
MSCDEX.EXE /D:MSCD001 /L:D
```

7. Move the `ebd.cab` file to your desktop. Then open it and copy it's contents to the floppy. (Make sure the cab file is not on the floppy when you copy the files, otherwise there won't be enough room on the floppy.)
8. Launch a DOS prompt (Start>Programs>Accessories>DOS Prompt) and run the following commands to make all files on the floppy read-write.
```
A:
attrib -S -H -R *.*
```
9. On your host machine, unzip the real mode DOS patch and put `WINMEDOS.COM` onto a ISO (IMGBURN is a good program for that). Mount it and figure out what drive letter your CD-ROM drive is. Then run the following commands in the DOS prompt:
```
A:
copy C:\Windows\System\REGENV32.EXE A:\
D:\WINMEDOS.COM (Assuming D: is your CD-ROM Drive. Ignore the instructions and just press any key when it prompts you to)
del REGENV32.EXE
```
10. (OPTIONAL) In VirtualBox, create a new floppy and format it in Windows. Then, browse to `C:\Windows\Command` and copy every file not ending in `.HLP`, `.BAT` or `.SYS` to the floppy. Once you've installed DOS to your hard disk using the steps above copy the contents of this new floppy to the `C:\DOS` directory.
11. Reinsert the boot floppy if you swapped it out. Now reboot from the floppy. You should now be in MS-DOS 8.1. Use the steps above to install to hard disk. You'll have to use method B for step 4.

## Files
- [MS-DOS 8.1 Boot Floppy](https://github.com/elijahr2411/mystuff/raw/main/dos81.img){:target="_blank"}
- [Utilities, to be copied to the DOS folder](https://github.com/elijahr2411/mystuff/raw/main/dosfolder.img){:target="_blank"}
