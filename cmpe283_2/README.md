# CMPE283 : Virtualization

#### Assignment 1: Instrumentation via Hypercell
#### Name:  Puneet Tokhi
#### Professor: Michael Larkin


### Questions

**Q1. For each member in your team, provide 1 paragraph detailing what parts of the lab that member
implemented / researched. (You may skip this question if you are doing the lab by yourself).**

**Ans:** I did it by myself

**Q2. Describe in detail the steps you used to complete the assignment. Consider your reader to be someone skilled in software development but otherwise unfamiliar with the assignment. Good answers to this question will be recipes that someone can follow to reproduce your development steps.**
    
 **Ans** The goal of this assignment was to learn about the KVM hypervisor and how to modify processor instruction behavior inside the KVM Hypervisor. I initially tried to find the total number of exits using a global variable as explained in canvas video but I ran into cycle dependancy errors so had to modify my code.  
 
**Prerequisites for the assignment:**
Linux machine set up and configuration as Assignment 1

**Steps to produce the desired output:**
1. Worked on the same Linux machine as Assignment 1
2. Modified the files `cpuid.c` and `vmx.c` and added necessary variables for total exits and total time
3. Run the commands `make modules` and `make -j 8 modules` to build the Kernel
4. After that all the steps need to be completed as the root user starting with running the command `make INSTALL_MOD_STRIP=1 modules_install` to install and package the modules
5. Check if the kvm module for the hypervisor is loaded using `lsmod | grep kvm`. If it already loaded, then get rid of it using `rmmod kvm_intel`
6. Once that is done, use the `modprob kvm` command which will load th kvm if there are no errors in the code. Then, use the `lsmod | grep kvm` to see if the kvm is loaded
7. Once all the steps are done, the machine needs to be rebooted.
8. After rebooting, install the virtual manager using `sudo apt install virt-manager`
9. In the Virtual Manager, add a test file to check the number of exits and the number of cycles spent in the exit
10. THe other way to test is by installing cpuid package in the virtual manager by using `sudo apt install cpuid`
11. This is the final output:

**Q3. Comment on the frequency of exits- does the number of exits increase at a stable rate? or are there more exits performed during certain VM operations? Approximately how many exits does a full VM boot entail?**

**Ans** The number of exits seem to increase at a constant rate. Despite the VM being pretty much idle, the number of exits seem to increase about 1000-2000 exits everytime I checked using the cpuid package. The most number of exits were observed during the VM bootup which easily crossed more than half a million exits. The VM boot seemed to spent a lot of time in cycles and the exits were the largest compared to all other operations. 
