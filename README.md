# Assignment 1 - Rushil Shah - 015908789

1. Start you VMWare workstation
    - You can download the workstation player [here](https://www.vmware.com/products/workstation-player/workstation-player-evaluation.html)
2. Create a VM and make sure to enable nested virtualization for the Hypervisor. You can find the details to do so [here](https://communities.vmware.com/t5/Nested-Virtualization-Documents/Running-Nested-VMs/ta-p/2781466)
3. There are few configurations that you select while setting up VM
    - I gave 6GM of RAM for my 8GM laptop and 4 CPUs. (The more the better)
5. I used Linux 20.0.04 LTS version as the OS in the virtual machine. You can download it [here](https://ubuntu.com/download/desktop)
6. Once you have installed and started the OS, go to [this](https://github.com/torvalds/linux) repository and fork it in your own github account. This is the original linux kernal repository created by Linus Torvalds
7. Go to the terminal and clone the forked repository using ``` git clone ``` 
8. Once the repository is cloned, download MakeFile from [here](https://github.com/rushil1999/linux/blob/master/cmpe283_Rushil_Shah/Makefile) and .c file from [here](https://github.com/rushil1999/linux/blob/master/cmpe283_Rushil_Shah/cmpe283-1.c) 
9. Open the terminal in parent directory where you cloned the repository and run ``` make ``` command to run the Makefile
10. You might end up with license and version based errors. Go to clones linux directory and run ``` cp /boot/config-5. ``` to get the versions of kernel
11. Now there are few libraries and packages that are required for the next steps 
    - ```sudo apt install make```
    - ```sudo apt install gcc```
    - ```sudo apt install dwarves```
    - ```sudo apt install flex```
    - ```sudo apt install bison```
    - ```sudo apt install libssl-dev``` 
12. Now in order to run some make commands, we need to create a .config file having contents of the specific kernel version config file. In my case I had config-5.13.0-39-generic

13. ![Alt Text](https://github.com/rushil1999/linux/blob/master/img1.jpeg)

14. Run ``` cp /boot/config-5.13.0-39-generic .config ``` inside linux directory
15. Now go to .config file and comment out CONFIG_SYSTEM_TRUSTED_KEYS and CONFIG_SYSTEM_TRUSTED_KEYRING and change the value of CONFIG_SYSTEM_REVOCATION_KEYS to "" 
16. Run ''' make oldconfig```. Just press enter to input default values in a series of questions asked
17. In previous step the version that we are currently running is different from the one checked out and so the the oldconfig make command will ask for only the new things that are no in the previous version
18. Run ```make prepare```
19. Run ```make -j x modules``` where x is number of cpu's that you have ( 4 in my case)
20. Run ``` make -j 4```. This builds the real kernel

21. ![Alt Text](https://github.com/rushil1999/linux/blob/master/img7.jpeg)
22. Run ``` sudo make INSTALL_MOD_STRIP=1 mdoules_install ```. This will strip away the debussing informaiton that is not needed to recude the computational load while installing the kernel
23. Run ```sudo make install```
24. Now go to cmp283-1.c file and add MODULE_LICENSE("GPL V2"); to get ride of the licensing error
25. Finally, run ```sudo reboot``` to let the OS selest the most recent version of kernel module which is 5.18 in my case and earlier it was 5.13.0-39-generic
26. Now run ```make``` to get the .ko kernel object

27. ![Alt Text](https://github.com/rushil1999/linux/blob/master/img2.jpeg)
27. To load this file run ``` sudo insmod cmpe 283-1.ko```
28. The earlier step won't give any output in console. To view the output run ```dmesg```
29. The console will have lot of lines displayed from which Pinbased Controls MSR value is the answer of the assignment. If at all it shows 9, it implies that the nested virtualization hasn't been enabled and you may have to start fresh.

30. ![Alt Text](https://github.com/rushil1999/linux/blob/master/img4.jpeg)
31. To remove the kernel object simple run ``` sudo rmmod cmpe283-1.ko```
32. If you want to make any changes to the kernel module, you can simply remove it, make the changes, make the module and simply re-install it instead of rebooting the system.
33. This marks the completion of assignment, rest other part was to commit the Makefile and cmpe283-1.c to git.


# Assignment 2 & 3 - Rushil Shah - 015908789

1. Go to forked linux directory from the previous assignment
2. run ```cd linux/arch/x86/kvm``` 
3. Include the code for the implementation in cpuid.c and vmx/vmx.c.
4. Once the code is added, run ``` make -j 4 modules```
5. Once that is done run ``` sudo make INSTALL_MOD_STRIP=1 modules_install
6. run ```sudo make install```
7. Now check if kvm is not loaded in the hypervisor for some reason, to do that run ```lsmod | grep kvm```
8. If you find kvm and kvm_intel like the image below run ``` sudo rmmod kvm_intel ``` and ``` sudo rmmod kvm``` to remove the modules
9. Once removed, check again and if there is no output you can again load the updated module.
10. ![Alt Text](https://github.com/rushil1999/linux/blob/master/img6.jpeg)

11. To load the updated module, run ``` sudo modprobe kvm ``` and ``` sudo modprobe kvm_intel```
12. Now to verify if our code runs correctly Ubuntu VM instance inside the current VM is needed, which can be done by installing the Virtual Manager
13. run ``` sudo apt update ```
14. run ```sudo sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virtinst virt-manager```
15. run ```sudo systemctl is-active libvirtd ```. This will check if the daemon is active or not
16. To start the virtual manager, run ```virt-manager```. This will boot where in you will have to use .iso file to install ubuntu
17. Post installation, open the terminal and run ```sudo apt-get update``` and install cpuid by ``` sudo apt-get install -y cpuid```
18. cpuid package is installed to test our implementation in the module, this lets us directly run by giving the input and see the output
19. Run commands``` cpuid(0x4FFFFFFF)```,``` cpuid(0x4FFFFFFE)```,``` cpuid(0x4FFFFFFD)``` and ``` cpuid(0x4FFFFFFC)``` to run the functions
20. Go back the existing VM and run ``` dmesg ``` to get the output.

PS: During running ```make INSTALL_MOD_STRIP=1 modules_install``` I came across cycle detection error

![Alt Text](https://github.com/rushil1999/linux/blob/master/img5.jpeg)


# Assignment 4 - Rushil Shah - 015908789

Steps
1. Conitnue with Assignment 3 environment
2. Run ```cpuid(0xFFFFFFE) -s 31``` in the inner VM and record the exit count information
3. Shutdown inner VM, using the normal OS shutdown
4. In the outer VM, remove the kvm-intel module, by running the command ``` sudo rmmod kvm_intel ```
5. Reload the module, with ept = 0 parameter, by running ```sudo insmod kvm-intel.ko ept=0``` to disable nested pagindg and force shadow paging
6. Boot the inner VM again
7. Repeat step 2 and record the output

Q3. Shadow paging has a higher exit count than nested paging because the VMM has to do more work in shadow paging.

Q4. When shadow paging is enabled (ept=0), the VM conducts more TLB flushes, page faults, and other operations, resulting in more exits than when ept=1.
