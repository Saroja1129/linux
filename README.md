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

Next we will install Nested VM.


And then inner VM (ubuntuu) was created inside existing VM by installing Virtual manager using below commands:
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

# Assignment-3 
Here I worked on two leaf nodes %eax=0x4FFFFFFD and %eax=0x4FFFFFFC.
For CPUID leaf node %eax=0x4FFFFFFD: <br/>
 1.The leaf node should return the number of exits for the exit number given in %ecx. 
 2.The output should return %eax.
 3. Executed the CPUID package with 0x4FFFFFFD and exit the number in ecx.<br/>
 
 For CPUID leaf node %eax=0x4FFFFFFC:
 Modified the code in cupid.c and vmx.c
     1. To get the high 32 bits of the total time spent in the exit in %ebx.
     2. And the the low 32 bits of the time spent in at exit %ec.
     3. Executed the CPUID package with 0x4FFFFFF and exit number in ecx.
     
The setup for this asssignment is same as the previous one the only change is we have to write code for the above instructions to handle the respective requests.
We executed the inner commands in the inner VM which is inside another VM
The commands are:
cpuid -l 0X4ffffffc -s exit_number
cpuid -l 0X4ffffffd -s exit_number

--The code is in assignment-3 folder.

<br/>
## Questions
3. Comment on the frequency of exits – does the number of exits increase at a stable rate? Or are there more exits performed during certain VM operations? Approximately how many exits does a full VM boot entail? 
I noticed that the frequency began to increase. And there was a direct difference in the increase in frequency of the number of exits before and after booting the nested VM. The full VM boot entailed around ~6900000.


I observed that the frequency continued to increase. And I saw a clear difference in increase before and after booting the nested VM. Approximately a full VM boot entail around ≈ 7000000 exits. 


4. Of the exit types defined in the SDM, which are the most frequent? Least? <br/><br/>

List of most frequent exit numbers

Exit Number 12 - HLT
Exit Number 30 - I/O instrcution
Exit Number 1 - External Interrupt
Exit Number 10 - CPUID
Exit Number 48 -EPT violation

List of least Frequent Exit Numbers,

Exit Number 0 - Exception
Exit Number 7 - Interrupt window
Exit Number 29 - MOV DR
Exit Number 55 - XSETBV 

# Assignment 4

Steps:
- Execute the assignment 3 code and boot a test VM using that code.
- Once the VM has booted, we will run the cpuid to get the total number of exits count for each type of exit handled by the KVM for both nested and shadow paging for this we will run the sequence of queries of CPUID leaf function 0x4FFFFFFE.
- Shutdown your test (inner) VM.
- Remove the ‘kvm-intel’ module from your running kernel
- Reload the kvm-intel module with the parameter ept=0 
- The module is present in the path insmod /lib/modules/5.15.0+/kernel/arch/x86/kvm/kvm-intel.ko. 
-  Boot the VM again, and capture the same output as you did in step 2. <br/> <br/> <br/>


## Questions
1. For each member in your team, provide 1 paragraph detailing what parts of the lab that member implemented / researched. (You may skip this question if you are doing the lab by yourself). <br/>
I am doing this myself <br/> <br/>

2. Include a sample of your print of exit count output from dmesg from “with ept” and “without ept”. <br/>

With EPT <br/>

![with ept (1)](https://user-images.githubusercontent.com/25710427/145280844-ecb31762-a9f4-4a34-91a6-57ae42359649.png)<br/>
![with ept (2)](https://user-images.githubusercontent.com/25710427/145280877-c12467bc-d759-40cd-b6ea-00a87e5127bb.png) <br/> <br/>

Without EPT <br/>
![no ept (1)](https://user-images.githubusercontent.com/25710427/145280915-69a246c5-8b99-46e4-b891-fa0317ae206d.png) <br/>
![no ept (2)](https://user-images.githubusercontent.com/25710427/145280936-6e1ac391-9a43-4e21-89dc-5beab1a15d2d.png) <br/> <br/>


3. What did you learn from the count of exits? Was the count what you expected? If not, why not? <br/>
After Reboot, the number of exits increased. This was something I expected to happen because Shadow Page Architecture wants more exits than Nested Page Architecture.
 <br/> <br/>

4. What changed between the two runs (ept vs no-ept)? <br/>
No-ept generates extra exits for each memory access. Because with Shadow Paging, a more number of exits, controls must be turned on for each memory access to perform properly. VMM, on the other hand, is unconcerned about any of this in Nested since VMM protects the secondary translation of each memory access.  <br/><br/>











