# EFI Hot Swap
If you are like me then you'll have a habit of hot-swapping whole drives at a time. At one point, there is a UEFI operating system that your motherboard won't automatically detect if you take it out and put it back in. 

## Why this happens
There is a very simple reason for this: UEFI can't find the EFI file. The [UEFI Standard](https://uefi.org/sites/default/files/resources/UEFI_Spec_2_8_final.pdf) has very particular rules on exactly **where** this EFI file is supposed to be and some operating systems don't have the capability of hot-swapping it. According to section **3.5.1.1 Removable Media Boot Behavior**, UEFI expects the EFI file of hot-swappable media to be `EFI\BOOT\BOOTx64.EFI`. And so, if your system does not support hot-swapping, you could probably go into `boot/` and copy all of the files into this directory and rename the EFI file. 


## Testing the correct EFI file
To test which EFI file is the right one, simply use a tool that can edit the boot manager of EFI. On linux, the most common tool is `efibootmgr`. For Qubes OS for example, you'd use:

```
efibootmgr -v -c -u -L Qubes -l /EFI/qubes/xen.efi -d /dev/sda -p 1
```

This command will add `/EFI/qubes/xen.efi` to the boot list of your UEFI firmware and name the entry `Qubes`. The EFI partition would be on partition 1 of `/dev/sda`. Sometimes `efibootmgr` replaces `/dev/sda partition 1` with the actual UUID of the partition so it isn't dependent on the order the motherboard sees the drives.
