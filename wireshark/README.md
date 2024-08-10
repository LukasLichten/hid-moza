# Capturing and Information gathered
This folder contains dumps of the communication with the wheelbase.  
Also contains instructions for dumping and findings.

## Capture Instructions
### Basic Setup
Install wireshark on your Linux distro, then go through [Wireshark USB Capture Setup](https://wiki.wireshark.org/CaptureSetup/USB).  
These instructions aren't great, for Arch users I suggest:  
- Install `wireshark-qt` (optionally `termshark`)
- Add your user to the wireshark group with `usermod -a $USER -G wireshark`
- Reboot (logout to sddm was not sufficient for me)
- `modprobe usbmon` before capturing (you have to redo it every boot)
  
You want to apply some sort of filter to avoid excessive chatter from Keyboard, Mouse, Audio, etc.  
Wireshark is odd with it's display filter seperate from the capture filter (and the syntax being misserable).  
My suggestion is to setup a display filter, then use `File>Export Specified Packets...` instead of `File>Save As`,
this allows you to select the Packet Range, per default it will be `All packets` and `Displayed`, this will clean the saved file for adding.  
  
A good filter is the `usb.addr`, you can use `lsusb` to find the bus and device, which form the first two elements of the addr.  
The addresse is a string, so you can use slicing with `[lower:higher]`.  
Example for bus 1 device 13: `usb.addr[0:4] == "1.13"`

### Windows VM setup
To capture the behavior communication from Pithouse/driver to the Wheelbase we need to capture the wheelbase a VM is vital.  

Setting up a Windows 11 VM can be quite treatures, with tpm and getting around account creation,
I wrote about the expierence on my [blog](https://blog.dergeneralfluff.duckdns.org/post/240726-cli-on-windows-sucks) (which maybe used as a guide).  
I am using `qemu` via `virt-manager`.  
The [Wireshark USB Capture Setup Guide](https://wiki.wireshark.org/CaptureSetup/USB) (mentioned above already)
advices to use a Linux-host/Windows-vm setup for any usb capturing on the plattform using VirtualBox.  
But the guide continues to be woefully out of date (`ifconfig` does not exist anymore), although VirtualBox can probably still be used
(with a similar setup, but from here on this text assumes qemu and virt-manager being used, adapt as needed).  
  
As the setup is dead simple anyway: Once you have a functioning VM, press `Add Hardware`, select `USB Host Device`, and select the Wheelbase.  
This will work even if the VM is already running.  
You can instead also use (from the menubar) `Virtual Machine>Redirect USB Device`, but this is only available while the VM is running.  
  
Capture works the same as if the device was used native, ergo any running capture will see a second usb handshake as soon as Windows is given the device.

  
### Extra
`dmesg` displays the kernel ring buffer messages, in a way the log messages from the kernel modules (I can already hear some kernel experts yell at me, but this makes the most sense).
  
The device won't produce any interrupts if no software is reading it. You can use `evtest` or `jstest` (packaged under Arch as `extra/evtest` and `extra/joyutils` respectively) for this.  
  
FFB testing tools exist in [ffbtools](https://github.com/berarma/ffbtools) and [FFBChecker](https://github.com/MadCatX/FFBChecker), *but I have not gotten to this point yet...*

## Findings
Testing is conducted (If not otherwise specified) on a Moza R5 with Moza ES Steering Wheel (aka the Moza R5 bundle), very useful as it is a very popular combination.  
Contributions of other configurations welcome.  
  
Current state with `hid-generic` it detects the wheelbase, systemd gives uaccess automatically, it is useable in 540deg mode, limp (no feedback), buttons work (dpad appears as buttons).  

`evtest` output:
```
Input driver version is 1.0.1
Input device ID: bus 0x3 vendor 0x346e product 0x4 version 0x111
Input device name: "Gudsen MOZA R5 Base"
Supported events:
  Event type 0 (EV_SYN)
  Event type 1 (EV_KEY)
    Event code 288 (BTN_TRIGGER)
    Event code 289 (BTN_THUMB)
    Event code 290 (BTN_THUMB2)
    Event code 291 (BTN_TOP)
    Event code 292 (BTN_TOP2)
    Event code 293 (BTN_PINKIE)
    Event code 294 (BTN_BASE)
    Event code 295 (BTN_BASE2)
    Event code 296 (BTN_BASE3)
    Event code 297 (BTN_BASE4)
    Event code 298 (BTN_BASE5)
    Event code 299 (BTN_BASE6)
    Event code 300 (?)
    Event code 301 (?)
    Event code 302 (?)
    Event code 303 (BTN_DEAD)
    Event code 704 (BTN_TRIGGER_HAPPY1)
    Event code 705 (BTN_TRIGGER_HAPPY2)
    Event code 706 (BTN_TRIGGER_HAPPY3)
    Event code 707 (BTN_TRIGGER_HAPPY4)
    Event code 708 (BTN_TRIGGER_HAPPY5)
    Event code 709 (BTN_TRIGGER_HAPPY6)
    Event code 710 (BTN_TRIGGER_HAPPY7)
    Event code 711 (BTN_TRIGGER_HAPPY8)
    Event code 712 (BTN_TRIGGER_HAPPY9)
    Event code 713 (BTN_TRIGGER_HAPPY10)
    Event code 714 (BTN_TRIGGER_HAPPY11)
    Event code 715 (BTN_TRIGGER_HAPPY12)
    Event code 716 (BTN_TRIGGER_HAPPY13)
    Event code 717 (BTN_TRIGGER_HAPPY14)
    Event code 718 (BTN_TRIGGER_HAPPY15)
    Event code 719 (BTN_TRIGGER_HAPPY16)
    Event code 720 (BTN_TRIGGER_HAPPY17)
    Event code 721 (BTN_TRIGGER_HAPPY18)
    Event code 722 (BTN_TRIGGER_HAPPY19)
    Event code 723 (BTN_TRIGGER_HAPPY20)
    Event code 724 (BTN_TRIGGER_HAPPY21)
    Event code 725 (BTN_TRIGGER_HAPPY22)
    Event code 726 (BTN_TRIGGER_HAPPY23)
    Event code 727 (BTN_TRIGGER_HAPPY24)
    Event code 728 (BTN_TRIGGER_HAPPY25)
    Event code 729 (BTN_TRIGGER_HAPPY26)
    Event code 730 (BTN_TRIGGER_HAPPY27)
    Event code 731 (BTN_TRIGGER_HAPPY28)
    Event code 732 (BTN_TRIGGER_HAPPY29)
    Event code 733 (BTN_TRIGGER_HAPPY30)
    Event code 734 (BTN_TRIGGER_HAPPY31)
    Event code 735 (BTN_TRIGGER_HAPPY32)
    Event code 736 (BTN_TRIGGER_HAPPY33)
    Event code 737 (BTN_TRIGGER_HAPPY34)
    Event code 738 (BTN_TRIGGER_HAPPY35)
    Event code 739 (BTN_TRIGGER_HAPPY36)
    Event code 740 (BTN_TRIGGER_HAPPY37)
    Event code 741 (BTN_TRIGGER_HAPPY38)
    Event code 742 (BTN_TRIGGER_HAPPY39)
    Event code 743 (BTN_TRIGGER_HAPPY40)
    Event code 744 (?)
    Event code 745 (?)
    Event code 746 (?)
    Event code 747 (?)
    Event code 748 (?)
    Event code 749 (?)
    Event code 750 (?)
    Event code 751 (?)
    Event code 752 (?)
    Event code 753 (?)
    Event code 754 (?)
    Event code 755 (?)
    Event code 756 (?)
    Event code 757 (?)
    Event code 758 (?)
    Event code 759 (?)
    Event code 760 (?)
    Event code 761 (?)
    Event code 762 (?)
    Event code 763 (?)
    Event code 764 (?)
    Event code 765 (?)
    Event code 766 (?)
  Event type 3 (EV_ABS)
    Event code 0 (ABS_X)
      Value    948
      Min   -32768
      Max    32767
      Fuzz     255
      Flat    4095
    Event code 1 (ABS_Y)
      Value -32767
      Min   -32768
      Max    32767
      Fuzz     255
      Flat    4095
    Event code 2 (ABS_Z)
      Value -32768
      Min   -32768
      Max    32767
      Fuzz     255
      Flat    4095
    Event code 3 (ABS_RX)
      Value -32767
      Min   -32768
      Max    32767
      Fuzz     255
      Flat    4095
    Event code 4 (ABS_RY)
      Value -32767
      Min   -32768
      Max    32767
      Fuzz     255
      Flat    4095
    Event code 5 (ABS_RZ)
      Value -32768
      Min   -32768
      Max    32767
      Fuzz     255
      Flat    4095
    Event code 6 (ABS_THROTTLE)
      Value -32768
      Min   -32768
      Max    32767
      Fuzz     255
      Flat    4095
    Event code 7 (ABS_RUDDER)
      Value -32767
      Min   -32768
      Max    32767
      Fuzz     255
      Flat    4095
    Event code 16 (ABS_HAT0X)
      Value      0
      Min       -1
      Max        1
    Event code 17 (ABS_HAT0Y)
      Value      0
      Min       -1
      Max        1
  Event type 4 (EV_MSC)
    Event code 4 (MSC_SCAN)
  Event type 21 (EV_FF)
    Event code 80 (FF_RUMBLE)
    Event code 81 (FF_PERIODIC)
    Event code 82 (FF_CONSTANT)
    Event code 83 (FF_SPRING)
    Event code 84 (FF_FRICTION)
    Event code 85 (FF_DAMPER)
    Event code 86 (FF_INERTIA)
    Event code 87 (FF_RAMP)
    Event code 88 (FF_SQUARE)
    Event code 89 (FF_TRIANGLE)
    Event code 90 (FF_SINE)
    Event code 91 (FF_SAW_UP)
    Event code 92 (FF_SAW_DOWN)
    Event code 96 (FF_GAIN)
```
  
Wheel axis is the X axis, all the other axis and buttons are likely reported to handle support of peripherals/otherwheel rims.  
We have EV_FF events, need to test if we can use those to apply the effects... but then we still need a way to set wheel rotation etc...

## Log Info
Most logs are generated from a Moza R5, with ES Steering Wheel, pedals disconnected.

### 090824-01-Linux-only-connect-and-jtest-left-right 
Moza wheelbase connects as `1.11`, some chatter from USB controllers is still left in the log.  
  
Test includes switch on and boot up of the wheelbase, and then a couple roations left right while `jstest` is polling the base.

### 090824-02-090824-02-Passthrough-Windows-Device-Added
Wheelbase connected as `1.13` (finally filtered with only it's traffic).

The wheelbase is already running, Windows VM is running, `USB Host Device` with the Wheelbase is added in qemu, causing the Handshake to run from Windows.  
  
No Wheeldriver is installed on the Windows VM, it is unable to read inputs using `Set up USB Game controllers` 
(although such passthroughs can read values from other game controllers, so the behavior could be windows, and could be fixed once drivers are installed). 

### 100824-01-Pithouse-Run-None-Functional
Wheelbase connected as `1.10`.  
  
Wheelbase already running, Windows VM too, with the device initally passed through with `Redirect USB Device`.  
Includes the installation of Pithouse (produces no chatter), first start.  
Pithouse reports as disconnected.  
Device is then removed from `Redirect USB Device` and moved to `USB Host Device`, no change.  
Restarted VM, no change.  
Updated Pithouse, no change.  

