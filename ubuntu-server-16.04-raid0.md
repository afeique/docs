**Created**: 17 July 2017

# Ubuntu Server 16.04 Installation with `mdadm` RAID0

This is a guide for installing Ubuntu Server 16.04 with software RAID0 using `mdadm`. It includes some unrelated but potentially useful miscellany relating to naming network interfaces at the end.

This guide presupposes knowledge of downloading ISOs and using [Rufus](https://rufus.akeo.ie/) to create a bootable USB stick, or burning the ISO directly to a DVD.

## Software RAID0 using `mdadm`

See:

* [Ubuntu 16.04 Server Guide: Advanced Installation](https://help.ubuntu.com/lts/serverguide/advanced-installation.html#software-raid).
* [Wikipedia: Standard RAID levels](https://en.wikipedia.org/wiki/Standard_RAID_levels).

Follow the installation steps until you get to the `Partition Disks` step, then:

* Choose `Manual` as the partition method.

For every drive you wish to make part of the RAID0 array:

* Select the drive and `Create a new empty partition table on this device`.
* Select the `FREE SPACE` on the drive and then `Create a new partition`.
* Select the size of the partition. This partition will be the swap partition, and a general rule for swap size is twice that of RAM. Enter the partition size, then choose `Primary`, then `Beginning`. 
    * **Note**: A swap partition size of twice the available RAM capacity may not always be desirable, especially on systems with large amounts of RAM. Calculating the swap partition size for servers is highly dependent on how the system is going to be used.
* Select the `Use as` line at the top. By default this is `Ext4 journaling file system`. Change it to `physical volume for RAID` and then hit `Done setting up partition`.
* Now, select the remaining `FREE SPACE` on the drive and `Create a new partition`. This partition will be used for the `/` mount point.
* Use the rest of the free space on the drive and choose `Continue`, then `Primary`.
* As with the swap partition, select the `Use as` line at the top and select `physical volume for RAID`. Change the `Bootable flag` to `on`. Finally, choose `Done setting up partition`.

Repeat the above steps using the for every drive you wish to make part of the RAID0 array.

**Note**: you can partition the drives however you want, i.e. put swap at the end or use a `Logical` partition instead of `Primary`, but all the drives must be partitioned identically with the same sizes and layout.

### Software RAID Configuration

With the partitions setup, the software RAID array is ready to be configured.

* Back in the main `Partition Disks` page, select `Configure Software RAID` at the top.
* Select `yes` to write changes to disk.
* Choose `Create MD device`.
* Select `RAID0`, but if you are using a different setup choose the appropriate type (RAID0 RAID1 RAID5).
    * In order to use RAID5 you need at least three drives. With RAID0 or RAID1, only two drives are required.
* Choose which partitions to use. Generally they will be `sda1`, `sdb1`, `sdc1`, etc. The numbers will usually match and the different letters correspond to different hard drives.
* For the swap partition, choose `sda1` and `sdb1`. Make sure to pair up partitions by size. Select `Continue` to go to the next step.
* Repeat these steps for the `/` partition, this time choosing `sda2` and `sdb2`. Again, make sure to pair up partitions by size.
* Once done, select `Finish`.

### Formatting

There should now be a list of hard drives and RAID devices. The next step is to format and set the mount point for the RAID devices. Treat the RAID device as a local hard drive, format and mount accordingly.

* Select `#1` under the `RAID1 device #0` partition.
* Select `Use as` and set to `swap area`, then `Done setting up partition`.
* Next, select `#1` under the `RAID1 device #1` partition.
* Select `Use as` and set to `Ext4 journaling file system`, then `Done setting up partition`.
* Select the `Mount point` and choose `/ - the root file system`. Change any of the other options as appropriate, then `Done setting up partition`.
* Finally, select `Finish partitioning and write changes to disk`.

If you choose to place the root partition on a RAID array, the installer will then ask if you would like to boot in a degraded state. See Degraded RAID for further details.

The installation process will then continue normally.

## Renaming Network Interfaces

See:

* [systemd/udev Predictable Network Interface Names](https://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/)
* [Understanding systemd Predictable Network Device Names](https://major.io/2015/08/21/understanding-systemds-predictable-network-device-names/)
* [Changing Network Interfaces Name Ubuntu 16.04](https://askubuntu.com/questions/767786/changing-network-interfaces-name-ubuntu-16-04)
* [How to Rename an Ethernet Interface](https://askubuntu.com/questions/217635/how-to-rename-an-ethernet-interface)

Ubuntu transitioned to a version of `systemd`/`udev` which uses [Predictable Network Interface Names](https://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/).

This results in network interfaces being named `enp6s0` instead of `eth0`, `eth1`, etc.

From the `udev` source code in `src/udev/udev-builtin-net_id.c`:

    /*
     * Predictable network interface device names based on:
     *  - firmware/bios-provided index numbers for on-board devices
     *  - firmware-provided pci-express hotplug slot index number
     *  - physical/geographical location of the hardware
     *  - the interface's MAC address
     *
     * http://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames
     *
     * Two character prefixes based on the type of interface:
     *   en -- ethernet
     *   sl -- serial line IP (slip)
     *   wl -- wlan
     *   ww -- wwan
     *
     * Type of names:
     *   b<number>                             -- BCMA bus core number
     *   ccw<name>                             -- CCW bus group name
     *   o<index>[d<dev_port>]                 -- on-board device index number
     *   s<slot>[f<function>][d<dev_port>]     -- hotplug slot index number
     *   x<MAC>                                -- MAC address
     *   [P<domain>]p<bus>s<slot>[f<function>][d<dev_port>]
     *                                         -- PCI geographical location
     *   [P<domain>]p<bus>s<slot>[f<function>][u<port>][..][c<config>][i<interface>]
     *                                         -- USB port number chain

As the naming convention goes: Ethernet cards will always start with en, but they might be followed by a `p` (for PCI slots), an `s` (for hotplug PCI-E slots), and `o` (for onboard cards).

### Using `GRUB_CMDLINE_LINUX`

This ensures NICs are named `eth0`, `eth1`, ..., `ethX` starting with the "primary" NIC (typically the motherboard NIC). An alternative approach using `udev/rules.d` is also provided below.

Edit `/etc/default/grub`. Change the line

    GRUB_CMDLINE_LINUX=""

to 

    GRUB_CMDLINE_LINUX="net.ifnames=0 biosdevname=0"

then run as root:

    $ sudo update-grub

and finally reboot:

    $ sudo reboot

### Using `/etc/udev/rules.d/70-persistent-net.rules`

**DOES NOT WORK**

This approach adds finer control to NIC naming by setting up `udev` naming rules based on NIC MAC addresses.

First, find the `udev` names and MAC addresses of the NICs you want to configure using:

    # udevadm info -e | grep ID_NET_NAME

Example output excerpt:

    E: ID_NET_NAME=enp1s0f1
    E: ID_NET_NAME_MAC=enxa0369f6e5227
    E: ID_NET_NAME_PATH=enp1s0f1

From this, we can gather that the MAC address is `a0:36:9f:6e:52:27`. To add a rule for naming this NIC to `eth0`, edit `/etc/udev/rules.d/70-persistent-net.rules` and add the following line:

    # Keep each entry on a single line

    SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="a0:36:9f:6e:52:27", ATTR{dev_id}=="0x0", ATTR{type}=="1", NAME="eth0"

The `/etc/udev/rules.d/70-persistent-net.rules` file could formerly be generated using `/lib/udev/write_net_rules`, but this seems to have changed in Ubuntu 16.04.

