# G14-VFIO
Files and scripts for a full VFIO on the ASUS Zephirus G14

These repo contains most (if not all) the files needed for a full VFIO on the Zephyrus G14.
I suggest reviewing the XML file (and the qemu script) for the VM as I installed windows in a physical disk, so in my use case, i need to do some mounting operations in order to keep guest and host on the same disk.
This allow the user to have the advantage to boot their VM os on bare metal hardware at will and have less overhead on the disk, as well as allowing to easily resize the partitions and better control the space used in the laptop.

Consider this a kinda weird of VM-DualBoot. You should really boot the VM instead of the physical disk, but the option is there in case you need to.
On arch linux a [bug](https://bugs.archlinux.org/task/67921) makes you unable to reproduce this setup. You need to use [libvirt-git](https://aur.archlinux.org/packages/libvirt-git)


## Explanation on files and relative paths

| File | Path | Description |
|-|-|-|
| win10.xml | n/a | Main file that defines the vm. Used by Qemu/libvirt to definte the Virtual Machine hardware and properties |
| SSDT.bin | /var/lib/libvirt/images/SSDT.bin | This file is used to emulate a battery in the guest vm and avoid a "Code 43" nvidia driver "bug" (consumer nvidia cards don't play well in vm) |
| looking-glass-client.ini | $HOME/.looking-glass-client.ini | This is the configuration file for looking-glass-client. Is used to define client's options without launching it with parameters |
| qemu | /etc/libvirt/hooks/qemu | This file is where all the magic happens. Is a simple script that detaches the nvidia card on the host when needed from the vm, as well as performing other automations |


## Other stuff you may need

You may still need to follow common guides for VFIO like you do on Desktops. Archwiki has a great guide you can follow to setup everything.
In this laptop particular case, the things you wanna watch out are these:
* You need to emulate a battery in the guest (using [SSDT.bin](../master/SSDT.bin)) and hide virtualization flags
* You need a dummy display over type-c (or a real one, if you prefer) as nvidia drivers NEED a screen attached in order to work. (without it looking glass won't work)
* You should note that the type-c port on the left is the one attached to the nvidia card. If you attach anything to it, the qemu script may have problems detaching it from your host.
* You may need a script to bind the nvidia graphics card to the VFIO driver. I used [this one](https://github.com/andre-richter/vfio-pci-bind)
* On arch and other systems, I suggest to install [asus-nb-ctrl](https://gitlab.com/asus-linux/asus-nb-ctrl) as it allows to switch fan modes, graphic options and other stuff that usually you can do on windows by using the armoury crate app.
* For lowest power consumption (5-8W) you may need to set your graphic mode to "integrated" using [asus-nb-ctrl](https://gitlab.com/asus-linux/asus-nb-ctrl)
* You may need [looking-glass](https://github.com/gnif/LookingGlass) with a dummy type-c plug or an external monitor (with type-c or type-c adapter) to see display output [CAUTION! If you connect a type-c monitor with charging capabilities and the power brick YOU WILL FRY THE MOTHERBOARD](https://www.reddit.com/r/ZephyrusG14/comments/inrdx3/usbc_power_delivery_power_adapter_issue/)
* You may need [scream](https://github.com/duncanthrax/scream) for audio passthrough. There are alternatives, but this is the one that works better.
* You may need to map your keys for switching keyboard grab in [looking-glass-client.ini](../master/looking-glass-client.ini) as I used printscr. Others may want to use other keys.
* Have a serious look at [asus-linux website](https://asus-linux.org/) as it cotains a ton of useful features and guides (and links to their discord too) for getting started with linux on ASUS laptops.


## Disclaimer

As for all things, I don't take any responsability for the stuff you will do by using these files and suggestions. This repository is provided as-is, without any warranty whatsoever.
