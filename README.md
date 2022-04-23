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
13. Run ``` cp /boot/config-5.13.0-39-generic .config ``` inside linux directory
14. Now go to .config file and comment out CONFIG_SYSTEM_TRUSTED_KEYS and CONFIG_SYSTEM_TRUSTED_KEYRING and change the value of CONFIG_SYSTEM_REVOCATION_KEYS to "" 
15. Run ''' make oldconfig```. Just press enter to input default values in a series of questions asked
16. In previous step the version that we are currently running is different from the one checked out and so the the oldconfig make command will ask for only the new things that are no in the previous version
17. Run ```make prepare```
18. Run ```make -j x modules``` where x is number of cpu's that you have ( 4 in my case)
19. Run ``` make -j 8```. This builds the real kernel
20. Run ``` sudo make INSTALL_MOD_STRIP=1 mdoules_install ```. This will strip away the debussing informaiton that is not needed to recude the computational load while installing the kernel
21. Run ```sudo make install```
22. Now go to cmp283-1.c file and add MODULE_LICENSE("GPL V2"); to get ride of the licensing error
23. Finally, run ```sudo reboot``` to let the OS selest the most recent version of kernel module which is 5.18 in my case and earlier it was 5.13.0-39-generic
24. Now run ```make``` to get the .ko kernel object
25. To load this file run ``` sudo insmod cmpe 283-1.ko```
26. The earlier step won't give any output in console. To view the output run ```dmesg```
27. The console will have lot of lines displayed from which Pinbased Controls MSR value is the answer of the assignment. If at all it shows 9, it implies that the nested virtualization hasn't been enabled and you may have to start fresh.
28. To remove the kernel object simple run ``` sudo rmmod cmpe283-1.ko```
29. If you want to make any changes to the kernel module, you can simply remove it, make the changes, make the module and simply re-install it instead of rebooting the system.
30. This marks the completion of assignment, rest other part was to commit the Makefile and cmpe283-1.c to git.
