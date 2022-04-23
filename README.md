# Assignment -1 

Name : Saroja Kandula.   
Student Id: 015944903.

1. Start VMWare workstation
2. You can download the workstation player here
3. Create a VM and make sure to enable nested virtualization for the Hypervisor. You can find the details to do so here
4. There are few configurations that you select while setting up VM
5. I gave 6GM of RAM for my 8GM laptop and 4 CPUs.
6. I used Linux 20.0.04 LTS version as the OS in the virtual machine.
7. Clone the github repo from Torvalds/Linux to VM.
8.Copy the .c and Makefile to linux path.
9. Execute Makefile command
10.You might end up with license and version based errors. Go to clones linux directory and run cp /boot/config-5. to get the versions of kernel
11.Now there are few libraries and packages that are required for the next steps
12. sudo apt install make
13. sudo apt install gcc
14. sudo apt install dwarves
15. sudo apt install flex
16. sudo apt install bison
17. sudo apt install libssl-dev
18. Create a .config file having contents of the specific kernel version config file. It's config-5.13.0-39-generic
19. In .config file and comment out CONFIG_SYSTEM_TRUSTED_KEYS and CONFIG_SYSTEM_TRUSTED_KEYRING and change the value of CONFIG_SYSTEM_REVOCATION_KEYS to ""
20. Execute  "make oldconfig". Input default values in a series of questions asked.
21. Execute make prepare
22. Execute make -j x modules where x is number of cpu's that you have ( 4 in my case)
23. Execute make -j 8. This builds the real kernel
24. Execute sudo make INSTALL_MOD_STRIP=1 mdoules_install. This will strip away the debussing informaiton that is not needed to recude the computational load while installing the kernel
25. Execute sudo make install
26. To resolve the license error add MODULE_LICENSE("GPL V2") in .c file.
27. Finally, run sudo reboot to let the OS selest the most recent version of kernel module which is 5.18 in my case and earlier it was 5.13.0-39-generic
28. Now run make to get the .ko kernel object
29. To load this file run sudo insmod cmpe 283-1.ko
30. The earlier step won't give any output in console. To view the output run dmesg
31. The console will have lot of lines displayed from which Pinbased Controls MSR value is the answer of the assignment. If at all it shows 9, it implies that the nested virtualization hasn't been enabled and you may have to start fresh.
32. To remove the kernel object simple run sudo rmmod cmpe283-1.ko
33. If you want to make any changes to the kernel module, you can simply remove it, make the changes, make the module and simply re-install it instead of rebooting the system.

# Assignment-2
## Instrumentation via Hypercall

1. In this assignment we will work on CPUID leaf nodes %eax=0x4FFFFFFF and %eax=0x4FFFFFFE
2. We return the total number of exits modified the code in cpuid.c file.
3. So, first we will Install the nested virtual machine inside the virtual machine
4. Now execute we will execute %eax=0x4FFFFFFF and %eax=0x4FFFFFFE in the eax and this will return the total number of exits.


The commands to execute are given below:
1. Modified the code in cpuid.c and vmx file.
2. make modules
3. make -j 4 modules
4. sudo bash
5. sudo make INSTALL_MOD_STRIP=1 modules_install && make install
6. lsmod | grep kvm
7. sudo rmmod kvm_intel
8. sudo rmmod kvm
9. modprobe kvm
10. modprobe kvm_intel

Next we will Create a Inner VM inside a VM using the below commands:
1. sudo apt update
2. sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
3. sudo systemct1 status libvirtd
4. sudo systemct1 enable --now libvirtd
5. sudo apt install virt-manager
6. sudo virt-manager
7. Install ubuntu 20.4 iso image
8. Installing CPUID package
9. Download the CPUID deb package for AMD64 https://packages.ubuntu.com/bionic/admin/cpuid
10. Installed the package and execute install using sudo dpkg -i cpuid_20170122-1.deb
11. Executed the below command inside VM
        cpuid -l 0X4fffffff -s exit_number
        cpuid -l 0X4ffffffe -s exit_number

I'm doing all assignments by myself.

