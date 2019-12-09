# DebuggingLinuxKernel

*Debugging the Linux Kernel with GDD, BuildRoot and QEMU*


I personally used BuildRoot to configure settings and compile the Linux Kernel, download Buildroot from:
https://buildroot.org/download.html

Create a directory and download buildroot to that directory, once inside, use command:
 
$make qemu_x86_defconfig

This will make and start running the config setup for buildroots x86 verison. 
Then run command:

$make menuconfig

To build and show the configs menu.

Go into the Build options ---> menu
hit enter on 'build packages with debugging symbols'

select the gcc debug level()---> menu
and hit enter on 'debug level 3'

go down and hit enter on 'strip target binaries'

go down to gcc optimiaztion level --->
- select 'optimize for debugging'

Exit back out to the main menu, and select Kernel option this time,
Ensure that your star is on the Linux Kernel, and that the Kernel version says ("Latest version (x.xxx)")


Ensure you save, and exit BuildRoot.

now you will need to make another config menu, and then start the virtual machine:
$make
$qemu-system-i386 -kernel output/images/bzImage -hda output/images/rootfs.ext2 -append "root=/dev/sda rw nokaslr" -s -S &

Ensure in your -append statement you have "nokaslr" as this will disable kaslrs randomized memory. 

Once your VM starts, you will need to then open another terminal in the samefolder your buildroot is in. 
You'll need to start up GDB in this new window, using;
$gdb

Once GDB starts up, and you see (gdb) in your console, you then need to setup the debugging symbols, by routing the file path inside of GDB, using;
$file ./output/build/linux-4.7.2/vmlinux

You will then need to attach GDB to the port on the system that the VM is running on:

$target remote :1234

We will then set a hardware break in GDB to run code, until it runs into the function start_kernel

$hbreak start_kernel

This will let the virtual machine boot up, running line by line, until it encounters the start_kernel function. 

From here, you can now step through the code using S or Step, or just use Continue, to let the code run normally.

You're now debugging the Linux Kernel using QEMU, GDB and Buildroot


For video of me running it, and how it should end up;
https://youtu.be/_AZlDxaXgYY
