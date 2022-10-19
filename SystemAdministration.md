## Administration

### Installation

In the beginning we will do a simple installation to get us up and running and come back around to better understand certain aspects. I do want to instill the idea that operating systems can and probably should be reinstalled often. 

Becoming comfortable moving between different operating systems and install has multiple advantages. 

The primary reason is if you do happen to pick up some kind of sleeper malware or other undesirable digital artifact when you start from scratch that attack vector will have to also start from square one as well.

Another reason is that it is a good exercise in making sure you know where all your data, software licenses are periodically can prevent unexpected surprises. As good system administrators we should always be looking to automate to make our jobs easier and less stressful. Our personal computing environments should be no different. Peace of mind knowing that you can get back up quickly if you have some kind of hardware failure is invaluable.

### Booting

There are really 4 stages to the boot process from power button to log in screen.

#### Firmware Stage

Firmware refers to low-level software that interfaces directly with all the hardware in your system. The `firmware stage` standard was originally called `BIOS` but the newer standard is called `UEFI`. You might hear it referred to as `BIOS` by some people. In fact many motherboards still support both. In most cases unless you are dealing with an older operating system you will not have to worry about it. But they are different standards. 

During this stage of the boot the system does basic checks of all the hardware and connected peripherals to make sure things are happy enough to proceed to the next stage of the boot. 

**If you have problems with getting passed this stage of the boot you might be able to upgrade the firmware for the system to fix the issue. Check the website of the manufacturer of your motherboard/system to see if this is an option.**

This is also the stage of the boot process that you must intervene in order to boot from different media (like a USB stick) if you want to install a new operating system. 

Each manufacturer has different keys that will do this. Dell typically uses `F2` to get into the `BIOS` only and then `F12` that will bring up a boot menu and let you select which device to continue the boot process using.

Once we have told the firmware which media we want to boot, the system will examine the beginning of the disk to determine what partitions are defined and which partition is `active` we can proceed to the next step. 

#### Bootloader Stage

Next, the system will look inside the `active` partition and in most all cases with linux will launch a program called `grub`

`grub` aka the `Grand Unified Bootloader` is responsible for loading the kernel into memory and starting it up. 

Like the BIOS/UEFI process. This process can also be paused and modified to perform emergency recovery or boot to completely different operating systems.

In fact you can have GRUB let you load either Windows or Linux at boot time if you have two Operating Systems installed.

This boot partitions can be on disk physically attached to the system or it could also be an image the is downloaded over the network via `TFTP` (trivial file transfer protocol) as is the case with `PXE` (pronounced pixie) or `Pre Execution Environment`. AKA Network booting.

[Great Overview of MBR boot process](https://neosmart.net/wiki/mbr-boot-process/)

Now that this initial kernel is loaded into memory, We are now ready to start the next stage:

#### Kernel Stage

The next stage can get tricky as while there is a running kernel, it probably does not have a way to talk to much of the system hardware (device drivers). Remember this is very basic kernel loaded by the bootloader stage. There is a separate partition that contains the kernel called `/boot`. 

The goal is to get the kernel up and running with device drivers loaded and the `/` filesystem mounted. This can be a problem if the currently running kernel does not have a way to talk to the device the `/` filesystem is on. 

There are two solutions to this problem.

One solution is to create a storage device in system memory (RAM) that can be used to add all the other bits of software needed by the kernel to get that `/` mounted and ready to go. This is called `initrd`.

The other solution is called `intramfs` and essentailly does the same thing but via a different method using ` CPIO` archive format.

[`initrd` or `intramfs`](https://www.baeldung.com/linux/initrd-vs-initramfs) will load the drivers and then remount `/`


Once all of this is finished the last task of this stage is to start `init`.


#### `init` Stage

`Init` is the first process and will always have  `pid` `1`. This is the part of the boot process that is responsible for getting everything else that the system needs to start to do its job up and going. 

There are different ways this can be accomplished.

Until relatively recently the most predominant design standard was called [`System V`](https://en.wikipedia.org/wiki/UNIX_System_V). Named because it was the predecessor to `System III`. 

There are also two new comers to this scene:

1. [Upstart](https://en.wikipedia.org/wiki/Upstart_(software))
2. [Systemd](https://en.wikipedia.org/wiki/Systemd)


Systemd has especailly come on strong and is the new defacto standard `init` system for all major linux distributions.

Like everything else in this world, history is very influential and many elements and command associated with `System V` are still present.

Depending on which `init` system is in use will determine which directories are examined to determine what needs to be started.

| `init` method | config directory |
| :---          | :--- |
| System V      | `/etc/init.d` |
| Upstart       | `/etc/init`   |
| Systemd       | `/etc/systemd` |


While `systemd` is by far the most common. It is good to know about the others so that if you run across a system using it you can figure out how to configure.

### Getting System Information

The most common command for getting a quick overview of what is going on in a system is the command `top`. It is most similar to `Task Manager` in Windows and does a really good job of giving you an idea of what the current CPU and system memory (RAM) usage is like. Let's give it a shot.

```bash
top
```

![top](./images/top.png)

There is also a command called `iotop` that will give you a similar view but instead of CPU and RAM it will show you disk input/output information. Another very common source of system speed degradation.

![iotop](./images/iotop.png)


One last command  in this series `iftop`. `if` stands for interface.  You often might want to know a quick overview of what is going on with your network connections. This is your tool.

![iftop](./images/iftop.png)


Note: In order to run `iotop`or `iftop` you must elevate to `root` privilege via the command `sudo`. We will talk more about `sudo` but it is the primary way we will gain `root` access.


If you are a Windows user you might be familiar with `System Information` and/or `Device Manager` to look at information about the hardware in your computer. Linux GUI's also have similar programs but as you might imagine there are command line ways to do all these things as well.

We have spent quite a bit of time looking at the `ls` command that is used to list files in a filesystem. In a similar vein there exist `ls` commands for your hardware that can give you quick information about your system. On your linux machine launch a terminal shell and let's try out a few of these.

```bash
lscpu | less
```

![lscpu less](./images/lscpu_less.png)

As you can see there is quite a bit of output but it will tell us about our CPU including the manufacturer and model. Here we have a 6 core AMD Ryzen chip that has 12 cpus (2 cpus per core).

Similarly if we want to see all the devices attached to the PCI bus we can use the `lspci` command:

```bash
lspci | less
```

![lspci less](./images/lspci_less.png)

Here we see things like storage controllers, network cards, video cards, lots of other lower level devices. This command can be useful when troubleshooting to see if a device is being detected by the system.

For the final command in this series we will look at `lsusb`. As you might imagine lists USB devices.

![lsusb](./images/lsusb.png)

Notice how many different root hub devices there are. 

### `/proc` filesystem

This is a special virtual filesystem that is created at boot time and is destroyed when the system shuts down. It contains lots and lots of information about the running system including running processes. Here is an example:

![proc](./images/proc.png)


Notice all the number directories? Each one of these corresponds to the `PID` of a running process. Lets take a closer look.

![procdir](./images/proc_dir.png)

Some of the files in `/proc` contain summary information. A great example of this is `/proc/meminfo`

![proc_meminfo](./images/proc_meminfo.png)

In fact many of the previous hardware-based `ls` commands use information from `/proc`. [Here is a great intro blog post](https://andythemoron.com/blog/2017-04-27/Proc-Filesystem).

Another useful and fast command is `free`. Free shows information about system memory. It reads it's  information from `/proc` as well.

```bash
free -h
```

![free_h](./images/free_h.png)


Looking at the hard drives attached to the system is another important task we might want to perform. The most straightforward way to look at the disks the system sees ia with the `lsblk` command.

```bash
lsblk
```

![lsblk](./images/lsblk.png)

### Kernel Modules

One of the coolest features about the linux kernel is it's use `modules`. `Modules` are just pieces of code that add additional functionality to the linux kernel. The best part about these modules is that they can be loaded and unloaded without rebooting the system. 

You can view the currently loaded `kernel modules` with... you guessed it: `lsmod`

```bash
lsmod
```

![lsmod](./images/lsmod.png)

As you can see there are lots of these things loaded in a running system. If you look through the list you will see things like `bluetooth` drivers for a Virtualization software called `VirtualBox` that is installed. Modules for video drivers can be seen as well.

`modinfo` is the command that will tell you more information about a particular module if you are not sure what it does.

```bash
modinfo nvidia | less
```

![modinfo](./images/modinfo.png)

Here we have the module that is responsible for `tls` security. That is the protocol that is used to encrypt your web traffic among other things.

The list is rather long so this is a great opportunity to use our friend the `pipe` (`|`) operator and filter for what we are interested in seeing. Let's say we want to know about any intel related modules that are loaded.

![tls_grep](./images/tls_grep.png)

As the output indicates there are several places that `intel` shows up in various modules. Awesome.

Loading and unloading these modules is accomplished with the command `modprobe`. Let's take a look with the `joydev` module that is responsible for interfacing with gaming joysticks.

 If we use the incantation from above we can see that the `joydev` module is currently loaded. 

```bash
lsmod | grep joydev
```

Since it is loaded let's proceed to unload it first.

```bash
modprobe -r joydev
```

Oops. This does not work. A regular user is able to list and view `kernel modules` but you must elevate to `root` to be able to load and unload `kernel modules`. Let's use `sudo` and try again.

```bash
sudo modprobe -r joydev
```

Now we have joy! Well technically no joydev but that is what we wanted. Silly us...

```bash
lsmod | grep joy
```

Confirmed... no joy. hahaha

Let's get it back

```bash
sudo modprobe joydev
```

Much better :)
 
 ![joydev_modprobe](./images/joydev_modprobe.png)

