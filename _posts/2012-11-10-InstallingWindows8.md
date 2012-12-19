---
date: 2012-11-14 13:23:18
title: Troubleshooting Windows 8 hang at Startup
layout: post
tags:
    - WINDOWS
categories:
    - Troubleshooting
---
The newborn Windows 8 can be viewed as Microsoft's latest effort to integrate all its screens, cellphones, PCs and Laptops, tablets and convertibles, and Xboxes.

The greatest change Windows 8 brought was the consistency across the screens. Settings and Live contents are automatically synced across all capable devices.

Apple users may say they were enjoying the same feature since the beginning of time, but we couldn't see MacOS and iThing share the same kernel as Windows-things do.

But Windows 8 is far, far from perfect, at least now. Numerous complaints (and unfortunately it seems I have experienced all the circumstances in the complaints), ranging from installing/updating to using/maintaining are there. Here're problems related to what I have encountered and their perspective, non-official solutions (I compiled them from multiple sources, like TechNet, MSDN).

## Windows Setup freezes at logo ##
This is a widely-seen problem, the simplest solution you should try is to update your BIOS. If you rushed into Windows Setup without updating the BIOS, you could try to disable your SD Card slot in BIOS and run the Windows Setup again.

If that didn't work, try to set your BIOS Sata mode to AHCI. Windows 8 works great on AHCI controllers so you don't need to insert a floppy and press F6 during setup. If your BIOS Sata Mode is already set to AHCI, switch it to IDE mode. There is a possibility that your motherboard does not support AHCI. If that didn't work and your computer worked well on AHCI before, then it isn't the problem of SATA mode. Set it back to AHCI.

If that still didn't work, it is the right time to check the installation media. Make sure the SHA value is right before burning it to a DVD. Also, try the new Windows 8 USB Setup, it's easy and convenient.

Check your RAM and HDD for any possible corruptions/bad sectors. Run MemTest86+ and SeaTool to verify them. As alternatives, you can run Windows Memory Diagnostic Tool and Check Disk in Windows. Grab a command line and type in `Memsched.exe` and `Chkdsk Drive: /r`. If any problems are found, please consider changing your components.

## Windows freezes at logo on start ##
This is another enthusiasm killer for Windows 8. After a good install of Windows Update or driver, the system may hang at startup, only showing the blue logo. There are many things that can contribute to this, generally, the troubleshooting steps are:

- Try the automatic repair. Automatic repair will automatically start if your computer failed to boot twice in a row.
- Enter safe mode (if you can. To enter safe mode, shut down your computer repeatedly during boot and wait for the automatic recovery. In `troubleshoot` and `advanced option`, choose `start up option`) and disable all non-Microsoft services and startup items, restart and verify if the problem is solved.
- If you can enter safe mode and that problem isn't solved, turn off Fast Start Up. To do this, choose `more power options` in power setting, in `choose what closing the lid does`, select `change settings that are unavailable`. Disable `Turn on fast start up`. `Shutdown` (Not `Restart`) and turn on your PC to verify if the problem is solved.
- Run `MemTest86+` and `SeaTool` to verify memory and disks.
- In many cases if the boot freezes, resetting your computer by pressing the power button will help. If this is the case, enter your Windows and update all drivers and BIOS and try the above steps. Drivers for ACPI, chipset, storage system are crucial, make sure they are updated.
- Check `event log` to see what happened/what problems occurred during boot.
- Disable Hyper-V and intel VT-d. Hyper-V and intel Virtualization Direct IO may be the cause. In your BIOS, disable them and verify if the problem is gone.
- Run `sfc /scannow` to verify that Windows System files are not tampered.
- If you eager to log into Windows, try a system restore and usually that will help.
- Do a clean installation of Windows 8, if you haven't given up by now.
- If you still cannot fix it, then it might be a problem with your computer or Windows 8. Live with the problem and constantly check for new updates. Do remember to voice in MSDN Forum so Microsoft will pay attention to this problem and collaborate with vendors to deal with it.

Note if you have more than 1 memory sticks in the slots, make sure you check them one by one. In some models, Windows Memory Diagnostics wouldn't run if your computer has multiple sticks.

I will keep updating this post if I'm experiencing more problems.
