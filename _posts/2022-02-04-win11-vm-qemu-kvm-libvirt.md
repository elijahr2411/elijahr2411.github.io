---
layout: post
title:  "Create a Windows 11 Virtual Machine with QEMU/KVM, Libvirt, and Virt-Manager"
---
Libvirt with QEMU/KVM is an excellent combination of tools that allows virtual machines to run much faster than VirtualBox, VMWare, and other virtualization suites. However, if you try to install Windows 11 in Virt-Manager using the default settings, you’ll probably get a compatibility error. This is because Windows 11’s system requirements are much stricter than previous versions of Windows and the default settings of virt-manager do not meet these requirements. This guide will help you to set up a working VM.

**NOTE: This guide assumes that you already have QEMU/KVM, Libvirt, and virt-manager setup and working.**
## System Requirements

As with your VM, your host system will also need to meet Windows 11’s system requirements, even if you don’t plan to ever run Windows 11 on it. Check them out here.

## The Steps

Without further ado, let’s get started.

1. Create a new Virtual Machine with at least 4GB of RAM and 64GB of storage, and the Windows 11 ISO mounted. MAKE SURE to check the “Customize Configuration Before Install” box. If you miss it, delete and recreate the machine and make sure to check it.
![Example of the above step](/assets/images/20220204newvm.png)
2. After clicking finish, a configuration screen will pop up. There are a few things you need to do here. The first is to set the firmware from BIOS to UEFI, as Windows 11 has dropped support for BIOS. There will be multiple options for UEFI. Select the one ending in `/x64/OVMF_CODE.secboot.fd`. This is necessary as Secure Boot is another requirement of Windows 11, and the install will continue to fail without it.
![Example of the above step](/assets/images/20220204biostype.png)
3. TPM is the last requirement of Windows 11 we need to fix. Make sure that your system has a TMP 2.0 module, otherwise, you won’t be able to install Windows 11. To add this to the machine, Click “Add Hardware” and TPM. Set the backend to Passthrough Device. Normally the default settings are fine, but if they don’t work you’ll need to figure out your TPM module’s block device and model. Click Finish to add the TPM module.
![Example of the above step](/assets/images/20220204tpm.png)
4. With these settings in place, click apply on the bottom-right corner and then Begin Installation on the top-left. The VM should open and Windows 11 should install as normal, and if all went well it will install.
![Example of the above step](/assets/images/20220204win11vm.png)
Thanks for reading and have a nice day!
