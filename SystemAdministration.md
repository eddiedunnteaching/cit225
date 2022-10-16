## Adminstration

### Getting System Information

The most common command for getting a quick overview of what is going on in a system is the command `top`. It is most similar to `Task Manager` in Windows and does a really good job of giving you an idea of what the current CPU and system memory (RAM) usage is like. Let's give it a shot.

```bash
top
```

![top](./images/top.png)

There is also a command called `iotop` that will give you a simlar view but instead of CPU and RAM it will show you disk input/output information. Another very common source of system speed degredation.

![iotop](./images/iotop.png)


One last command  in this series `iftop`. `if` stands for interface.  You often might want to know a quick overview of what is going on with your network connections. This is your tool.

![iftop](./images/iftop.png)


Note: In order to run `iotop`or `iftop` you must elevate to `root` priviledge via the command `sudo`. We will talk more about `sudo` but it is the primary way we will gain `root` access.


If you are a Windows user you might be familiar with `System Information` and/or `Device Manager` to look at information about the hardware in your computer. Linux GUI's also have similar programs but as you might imagine there are command line ways to do all these things as well.

We have spent quite a bit of time looking at the `ls` command that is used to list files in a filesystem. In a similar vein there exist `ls` commands for your hardware that can give you quick information about your system. On your linux machine launch a terminal shell and let's try out a few of these.

```bash
lscpu | less
```

![lscpu less](./images/lscpu_less.png)

As you can see there is quite a bit of output but it will tell us about our CPU including the manufacturer and model. Here we have a 6 core AMD Ryzen chip that has 12 cpus (2 cpus per core).

Similarly if we want to see all the dvices attached to the PCI bus we can use the `lspci` command:

```bash
lspci | less
```

![lspci less](./images/lspci_less.png)

Here we see things like NVME drives controllers, network cards, video cards, lots of other lower level devices. This command can be useful when troubleshooting to see if a device is being detected by the system.

For the final command in this series we will look at `lsusb`. As you might imagine lists USB devices.

![lsusb](./images/lsusb.png)

Notice how many different root hub devices there are. 

### `/proc` filesystem

This is a special virtual filesystem that is created at boot time and is destroyed when the system shuts down. It contiains lots and lots of information about the running system including running processes. Here is an example:

![proc](./images/proc.png)


Notice all the number directories? Each one of these corresponds to the `PID` of a running process. Lets take a closer look.

![procdir](./images/proc_dir.png)

Some of the files in `/proc` contain summary information. A great example of this is `/proc/meminfo`

![proc_meminfo](./images/proc_meminfo.png)

