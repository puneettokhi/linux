# CMPE283 : Virtualization

#### Assignment 3: Instrumentation via Hypercell
#### Name:  Puneet Tokhi
#### Professor: Michael Larkin


### Questions

**Q1. For each member in your team, provide 1 paragraph detailing what parts of the lab that member
implemented / researched. (You may skip this question if you are doing the lab by yourself).**

**Ans:** I did it by myself

**Q2. Describe in detail the steps you used to complete the assignment. Consider your reader to be someone skilled in software development but otherwise unfamiliar with the assignment. Good answers to this question will be recipes that someone can follow to reproduce your development steps.**
    
 **Ans** The goal of this assignment was to learn about the KVM hypervisor and how to modify processor instruction behavior inside the KVM Hypervisor. I initially tried to find the total number of exits using a global variable as explained in canvas video but I ran into cycle dependancy errors so had to modify my code.  
 
**Prerequisites for the assignment:**
Same linux machine set up and configuration as Assignment 2

**Steps to produce the desired output:**
1. Worked on the same Linux machine as Assignment 2
2. Modified the files `cpuid.c` and `vmx.c` and added necessary variables to keep count of the exit types and time processing the exits
3. Looked in the `SDM` for Appendix-C, table C-1 to find the total number of basic exit reasons, which turned out to be `69`
4. After modifying the files, I ran the same command as in Assignment 2 which were `make modules` and `make -j 8 modules` to build the Kernel
5. Then, ran the `make INSTALL_MOD_STRIP=1 modules_install` to install and package the modules
6. Checked if the kvm module for the hypervisor is loaded using `lsmod | grep kvm`. 
7. If it already loaded, then get rid of it using the `rmmod kvm_intel` command
8. Once that is done, use the `modprob kvm` command which will load the kvm if there are no errors in the code. 
9. Then, use the `lsmod | grep kvm` to see if the kvm is loaded
10. Once all the steps are done, the machine needs to be rebooted for the changes to take effect.
11. After rebooting, launch te inner VM and use the command `sudo apt install cpuid` to install the cpuid package in the inner VM
12. Once CPUID package is installed, I ran a for loop with leaf 0x4FFFFFFD until 69 which is the total number of basic exit types
    
    `for exits in seq 0 69; do cpuid -l 0x4ffffffd -s $exits; done`
13. The final output is displayed below:

|  ![Screenshot](/cmpe283_3/images/1.png)  |   ![Screenshot](/cmpe283_3/images/2.png)  


**Q3. Comment on the frequency of exits- does the number of exits increase at a stable rate? or are there more exits performed during certain VM operations? Approximately how many exits does a full VM boot entail?**

**Ans** Initially, I thought that the number of exits seemed to increase at a constant rate. However, later I realized that they don't seem to increase at a stable rate. The most number of exits were observed during the VM bootup which easily crossed over a million exits. The VM boot seemed to spent a lot of time in cycles and the exits were the largest compared to all other operations. Also, after doing some I/O operations like creating a file, the exits seem to increase significantly. Moreover, on running multiple commands, the exit seemed to increase at a faster rate.

**Q4. Of the exit types defined in the SDM, which are the most frequent? Least?**

**Ans** After looking at the output in dmesg, I noticed that the most frequent exits were exit type 28, 30, and 48. The highest number of exits was observed to be exit type 48 which is `EPT violation` type. According to the SDM, EPT violation is an attempt to access memory with a guest-physical address was disallowed by the configuration of the EPT paging structures. The next frequent violation in my machine was exit type 28(Control-register accesses) and exit type 30(I/O instruction).

Some of the least frequent exits on my machine were exit type 2(Triple fault), 3(INIT signal), 4(Start-up IPI (SIPI)), 5(I/O system-management interrupt (SMI)). There were other frequent exits as well such as exit type 25(VMWRITE), 26(VMXOFF), and 27(VMXON) which all turned out to be 0 on my machine.


