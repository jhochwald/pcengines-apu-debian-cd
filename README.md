Debian installer USB Stick for PC Engines APU board with mSATA drive
====================================================================

Introduction
------------
Based on the great work of Stanislav Sinyagin (ssinyagin) this repository
is a test to make it work a bit better in Germany and to adopt things for
my own servers.

Changes
-------
* Changed from i386 to amd64 installation
* Changed the partitioning incl. LVM
* Add a swap partition
* Extend the software selection
* Add an local User to use instead of root
* Remove clean text passwords

Create the ISO image
--------------------
```Shell
apt-get update && apt-get -V upgrade

updatedb

apt-get install -y simple-cdd git

mkdir /opt/pcengines
cd /opt/pcengines

git clone https://github.com/jhochwald/pcengines-apu-debian-cd.git .

build-simple-cdd --conf profiles/apu.conf --dist wheezy --force-root

dd if=images/debian-7.6-amd64-CD-1.iso of=/dev/sdb bs=1M
```

Create the USB Stick
--------------------
```Shell
# check where your USB stick is
fdisk -l

# copy the installer CD image
dd if=images/debian-7.6-amd64-CD-1.iso of=/dev/sdX bs=1M
```

Install
-------
Attach the USB Stick to your APU Server and power it on.
You might need to press F12 to change the boot order!

You might get the following message:
```
Undefined video mode number: 314
Press <ENTER> to see video modes available, <SPACE> to continue, or wait 30 sec
```
Just ignore it (wait 30 seconds) or press space.

Hint
----
At the moment, my ISO image is configured to wait after the installation is done.
I use this to remove the USB Stick before I reboot the APU.
You might want to change this by changing the following line:
```Shell
d-i debian-installer/exit/halt boolean true
```
to
```Shell
d-i debian-installer/exit/halt boolean false
```
You will find this line here: profiles/apu.preseed

Please note, that this work is licensed under the MIT License (MIT) license and
that there is no Warranty, and I mean absolutely no warranties!!!

This is a fork of: https://github.com/ssinyagin/pcengines-apu-debian-cd

NO WARRANTY
-----------
BECAUSE THIS PROGRAM IS AVAILABLE FREE OF CHARGE, THERE IS NO WARRANTY
FOR THE PROGRAM, TO THE EXTENT PERMITTED BY APPLICABLE LAW.  EXCEPT WHEN
OTHERWISE STATED IN WRITING THE COPYRIGHT HOLDERS AND/OR OTHER PARTIES
PROVIDE THE PROGRAM "AS IS" WITHOUT WARRANTY OF ANY KIND, EITHER EXPRESSED
OR IMPLIED, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE.  THE ENTIRE RISK AS
TO THE QUALITY AND PERFORMANCE OF THE PROGRAM IS WITH YOU.  SHOULD THE
PROGRAM PROVE DEFECTIVE, YOU ASSUME THE COST OF ALL NECESSARY SERVICING,
REPAIR OR CORRECTION.
 
IN NO EVENT UNLESS REQUIRED BY APPLICABLE LAW OR AGREED TO IN WRITING
WILL ANY COPYRIGHT HOLDER, OR ANY OTHER PARTY WHO MAY MODIFY AND/OR
REDISTRIBUTE THE PROGRAM AS PERMITTED ABOVE, BE LIABLE TO YOU FOR DAMAGES,
INCLUDING ANY GENERAL, SPECIAL, INCIDENTAL OR CONSEQUENTIAL DAMAGES ARISING
OUT OF THE USE OR INABILITY TO USE THE PROGRAM (INCLUDING BUT NOT LIMITED
TO LOSS OF DATA OR DATA BEING RENDERED INACCURATE OR LOSSES SUSTAINED BY
YOU OR THIRD PARTIES OR A FAILURE OF THE PROGRAM TO OPERATE WITH ANY OTHER
PROGRAMS), EVEN IF SUCH HOLDER OR OTHER PARTY HAS BEEN ADVISED OF THE
POSSIBILITY OF SUCH DAMAGES.

Here is the original README File of ssinyagin:
```
Debian installer CD for PC Engines APU board with mSATA drive
=============================================================

Introduction
------------

This is a set of configuration files for building a Debian installation
CD optimized for the APU platform of PC Engines:
http://www.pcengines.ch/apu.htm

This installation is only intended for an mSATA drive on the APU
board. If you plan to boot from SD card, use Voyage Linux instead:
http://linux.voyage.hk/

The build machine can be Debian Wheezy of amd64 or i386 architecture.

Before using these scripts, have a look at the files in profiles/, and
you may want to change some options.

The default hostname is set to "apu", unless DHCP delivers a different
name. Default root password is "pcengines", and it is defined in
"profiles/apu.preseed". If you want have a prompt for hostname, add the
following line to "profiles/apu.preseed":

d-i netcfg/get_hostname seen false

The disk layout does not allocate a swap partition (the APU board has 2
or 4GB RAM). If you need swap, you can add a swapfile in root
partition. The system is optimized for minimal swap usage:
vm.swappiness=1 in /etc/sysctl.d/pcengines_apu.conf

The profile adds an hourly cronjob
(/etc/cron.hourly/pcengines_apu_fstrim) which performs fstrim command on
root and boot partitions once a day.

The kernel boots with "elevator=deadline", which optimizes the I/O
performance for SSD drives.

See also SSD optimization tips:
https://wiki.freeswitch.org/wiki/SSD_Tuning_for_Linux
https://wiki.debian.org/SSDOptimization


Installation
------------

WARNING: this is a fully automated installation. Once you boot from the
USB stick, it will only give you 5 seconds in the initial menu, and the
rest will be done automatically, and a new Debian system will be
installed on mSATA SSD drive.

!!! ALL EXISTING DATA ON THE DRIVE WILL BE LOST !!!

The build machine needs to be Debian Wheezy. Other Debian versions or
Ubuntu are not fully tested and there are some issues, not yet fully
investigated.

Both the build machine and the APU board need the Internet connection
during the installation. The installer assumes that eth0 is connected to
a network with DHCP service and Internet access.

The installation ISO image is about 200MB in size.

Build the CD image on the build machine:

## install prerequisites
apt-get update
apt-get install -y simple-cdd git

## get our latest files
mkdir /opt/pcengines
cd /opt/pcengines
git clone https://github.com/ssinyagin/pcengines-apu-debian-cd.git .

## build the installation CD image
build-simple-cdd --conf profiles/apu.conf --dist wheezy --force-root

## insert the USB stick into the build machine

## check where your USB stick is
fdisk -l

## copy the installer CD image
dd if=images/debian-7.4-i386-CD-1.iso of=/dev/sdc bs=1M


## It is recommended to change the default boot sequence, and have mSATA
## drive as the first boot source. Upon booting the board, wait for F12
## prompt and press F12, and edit the default boot settings.

## Insert the USB stick into APU board and connect the serial terminal
## The terminal emulator should be vt220 or xterm (vt100 does not have
## F12). When the prompt for F12 appears, press F12 and select your USB
## stick as boot source.

## In the boot menu, "Install" will do automatic installation.

## ignore the error message and wait 30 seconds. I did not yet manage to
##   go around it:
Undefined video mode number: 314
Press <ENTER> to see video modes available, <SPACE> to continue, or wait 30 sec

## The installation continues, and you will get a new system within
## about 10 minutes. The APU will automatically reboot after the
## installation.

## If you want the board to halt after the installation is finished, add
## the following line to "profiles/apu.preseed":

d-i debian-installer/exit/halt boolean true


Author
------
Stanislav Sinyagin
ssinyagin@k-open.com
```