---
back: '/dos9x'
---
# PC-DOS 7.1

**Note: I now recommend MS-DOS 8.1 (The version included with Windows ME) over PC-DOS 7.1. Feel free to still use the information in this guide but you should also check out my page about MS-DOS 8.1.**

PC-DOS is IBM's version of DOS. I recommend it over MS-DOS 6.22 because it offers
FAT32 support, which is next to impossible to get on MS-DOS.

Unfortunately, 7.1, the version with FAT32 support, was not officially released.
 However, it is available on IBM's website, bundled with the
 "ServerGuide Scripting Toolkit, DOS edition". What is that? No idea, but it includes PC-DOS 7.1.
 You can download it [here](https://www.ibm.com/support/pages/ibm-serverguide-scripting-toolkit-dos-edition-version-1307){:target="_blank"}.
 Download the zip on that page and head to the `sgdeploy\sgtk\DOS` folder. There is PC-DOS 7.1.

## Installation
 You'll notice that all you get there are the files for an installation. You may ask,
 "how you do actually install it?". For that purpose, I've created a bootable floppy
 to boot into PC-DOS 7.1, using the files in that DOS directory. Get that at the bottom of the page.

 Once you've booted from that floppy, follow these instructions:
1. Partition your hard drive using fdisk.

NOTE: The included `fdisk32` in PC-DOS 7.1 is very buggy.
notably, it does not work with virtualbox. I recommend replacing it with [FreeDOS's fdisk](https://www.ibiblio.org/pub/micro/pc-stuff/freedos/files/repositories/latest/base/fdisk.zip).
My floppy includes this version for your convenience at `A:\FDISK.EXE`

2. Format your hard drive using `FORMAT /S` for FAT16, and `format32 /S` for FAT32.
The `/S` switch installs the DOS kernel and bootloader.
(Fdisk will tell you what FAT your partition is when you create it)

3. Copy the `AUTOEXEC.BAT`, `CONFIG.SYS`, and `DOS` directory to your hard disk.

4. Reboot and you should have a fully working DOS installation.

## Creating the boot floppy yourself
If you're like me and like making things yourself instead of downloading them from strangers on the internet,
follow this section to create the boot floppy yourself. You can then use it to install to your hard drive.

### You'll need:
- A virtualbox VM with 2 floppy drives and a hard disk

I find it so much easier to use a VM than a physical machine

- The PC-DOS 7.1 files. Information on how to get it from IBM above.
- A floppy image with the PC-DOS 7.1 files on it

Create the floppy with virtualbox, format it with an existing DOS installation,
then insert the files with PowerISO or a similar program
- An empty floppy image (also created with VirtualBox) (herein refered to as the install floppy)
- PC-DOS 2000 setup floppy, available from [WinWorld](https://winworldpc.com/download/c397c387-2b67-c2a4-0611-c3a6e280947e)

This is the last version of PC-DOS which a boot floppy was released. Since we'll need a live PC-DOS system to create the floppy,
this will do. Use PowerISO to insert [FreeDOS's fdisk](https://www.ibiblio.org/pub/micro/pc-stuff/freedos/files/repositories/latest/base/fdisk.zip)
to this floppy as `fdisk32` is broken on VirtualBox

### Inctructions
Without further ado...
1. Mount PC-DOS 2000 Setup Floppy 1 in the first floppy drive, and our install floppy in the second.
Start the VM.
2. Run `format /S B:` to format our install floppy and install the bootloader.
3. The `/S` switch will install the bootloader, which we need, but will also install a minimal PC-DOS 2000 setup.
Since we want 7.1, we'll need to delete this install. Run `attrib -S -H -R B:\`
to make these files writable, then run `del B:\*.*` to delete them.
4. With those gone, we can copy the files from PC-DOS 7.1. First, use `fdisk` and `format`/`format32`
to prepare our hard drive. We'll use this to store the files. Mount the floppy
 with the 7.1 files in drive B and copy them over to the hard drive with `xcopy B:\*.* C:\`,
  then switch the B drive back to our install floppy.
5. Now we need to copy the kernel. Copy `C:\IBMBIO.COM`, `C:\IBMDOS.COM`, and `C:\COMMAND.COM`
to the install floppy. I do not know why but they **must** be copied in that order.
```
COPY C:\IBMBIO.COM B:\
COPY C:\IBMDOS.COM B:\
COPY C:\COMMAND.COM B:\
```
6. With the kernel installed, copy `Autoexec.bat` and `Config.sys`
```
COPY C:\CFGFILES\AUTOEXEC.BAT B:\AUTOEXEC.BAT
COPY C:\CFGFILES\CONFIG.SYS B:\CONFIG.SYS
```
7. Now we have a very minimal PC-DOS 7.1 install on our floppy. To complete the install,
copy the rest of the files to the DOS directory on our install floppy.
 delete the files you've already copied from the C: drive to avoid copying them again
```
DELTREE C:\CFGFILES
DEL C:\IBMBIO.COM C:\IBMDOS.COM C:\COMMAND.COM
MKDIR B:\DOS
COPY C:\*.* B:\DOS
```
8. And done! remove all floppies and mount your install floppy to the A: drive,
then reboot. you should now have a usable PC-DOS 7.1 boot floppy that you can
later use to install it to a hard drive.
## Files

- [My PC-DOS 7.1 Boot Floppy](https://github.com/elijahr2411/mystuff/blob/main/pcdos71boot.img?raw=true)
