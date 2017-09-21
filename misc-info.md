[![MKS Instruments, LLC](img/mks/logo.png "MKS Instruments")](https://www.mksinst.com/)

# Miscellany

This is a collection of miscellaneous supplemental information. This information helps provide a better understanding of documented topics for people unfamiliar with the technologies.

## RAID

See:

* [Ubuntu 16.04 Server Guide: Advanced Installation](https://help.ubuntu.com/lts/serverguide/advanced-installation.html#software-raid).
* [Wikipedia: Standard RAID levels](https://en.wikipedia.org/wiki/Standard_RAID_levels).

Redundant Array of Independent Disks "RAID" is a method of using multiple disks to provide different balances of increasing data reliability and/or increasing input/output performance, depending on the RAID level being used.

## RAID 0

RAID 0 (also known as a stripe set or striped volume) splits ("stripes") data evenly across two or more disks, without parity information, redundancy, or fault tolerance. 

Since RAID 0 provides no fault tolerance or redundancy, the failure of one drive will cause the entire array to fail; as a result of having data striped across all disks, the failure will result in total data loss. This configuration is typically implemented with speed as the primary goal. 

RAID 0 is normally used to increase performance, although it can also be used as a way to create a large logical volume out of two or more physical disks.

RAID is implemented in either software or hardware.

When implemented in software, the operating system knows about both drives and actively maintains both of them. 

When implemented in hardware, a special RAID controller abstracts the RAID 0 setup and only a single hardware storage device is presented to the operating system.

The RAID software included with current versions of Linux and Ubuntu is based on the `mdadm` driver and works very well, better even than many so-called 'hardware' RAID controllers.