
# CMPE283 : Virtualization

## Assignment 1: Discovering VMX Features
#### Name:  Puneet Tokhi
#### Professor: Michael Larkin


### Questions

**Q1. For each member in your team, provide 1 paragraph detailing what parts of the lab that member
implemented / researched. (You may skip this question if you are doing the lab by yourself).**

**Ans:** I did it by myself

**Q2. Describe in detail the steps you used to complete the assignment. Consider your reader to be someone skilled in software development but otherwise unfamiliar with the assignment. Good answers to this
    question will be recipes that someone can follow to reproduce your development steps.**
    
 **Ans** The goal of this assignment was to create a linux kernel module to query the VMX features present in the processor. To visualize the desired output, `dmesg` command was used at the end.

**Prerequisites for the assignment:**
1. A machine capable of running Linux
2. VMX virtualization features enabled in the machine

**Steps to produce the desired output:**
1. Configure the Linux machine and enable virtualization pass through to the VM. In my case, I already had the Ubuntu machine so there was no VM required and I did the assignemnt on bare hardwre.
2. Use the `cat /proc/cpuinfo` command to see if hardware assisted virtualization enabled. The `flags` would denote if virtualization is enabled. In my case, since I was using my Ubuntu machine, the flags were already present
![Screenshot](/images/1.png)
3. Use the `uname -a` command to check the machine's kernel version
![Screenshot](/images/2.png)
4. Check the specifications of the machine using `df -h` command
![Screenshot](/images/3.png)
5. Install git using `sudo apt install git` command 
6. Download and build the Linux kernel source code by forking the Linux repository at `github.com/torvalds/linux.git` to your own repository
7. After forking the repo, clone the repo using `git clone` command
![Screenshot](/images/4.png)
8. Once the repository is cloned, download the required files from canvas: "cmpe283-1.c" and "Makefile"
9. Modify the download C file according to the assignment requirements using any editor. USe the SDM and modify the existing code and add new code for Pinbased controls, Procbased controls, VM Exits controls and VM Entry controls
10. After modifying the C file, go back to bash and `cd` to linux directory and type `make menuconfig` command. 
![Screenshot](/images/5.png)
11. After that, copy the config file using `cp /boot/config` based on the kernel version and create a new config file
12. Then, use the `make oldconfig` command which will prompt you to answer multiple questions. To get the default answer, press the Enter key
13. Once process is completed, use the `make prepare` command. 
14. After that, use the `make` command by `cd` to the directory where Makefile was downloaded. If there are errors, it could be because of a version mismatch related to the kernel version checked out from git.
15. In that case, use `make -j 8 modules` and once the step is completed, build the kernel using `make -j 8` command.
![Screenshot](/images/6.png)

![Screenshot](/images/7.png)

16. Next step would be to package the kernel and modules by typing `sudo make INSTALL_MOD_STRIP=1 modules_install`
17. After that, type the command `sudo make install` to install the kernel
18. Reboot the system and verify the new kernel version using `uname -a` 
19. Finally, run the `make` command using Makefile downloaded from canvas
![Screenshot](/images/13.png)
20. Once that step is successfully completed, check if the kernel file was installed using `ls *ko` and if installed, use the `sudo insmod 283-1.ko` command to insert the modules
21. There would not be any output on the screen because `lsmod grep | cmpe283` command shows that the module was already loaded, but since the Kernel module doesn't print to the console window, we have to use the `dmesg` command to see the output.
![Screenshot](/images/12.png)
22. Final Output would be displayed as:
![Screenshot](/images/8.png)
![Screenshot](/images/9.png)
![Screenshot](/images/10.png)
![Screenshot](/images/11.png)
23. In case the MSR values return a 0, it means that the hardware assisted virtualization feature was not enabled in the VM.
24. The complete output of the `dmesg` command is in the `output.txt` file in the `cmpe283_1` directory
25. The modified C file and other files for Assignment 1 are in the `cmpe283_1` directory


-----------------------------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------------------

## Assignment 2: Instrumentation via Hypercell
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
![Screenshot](/cmpe283_2/images/1.png)
![Screenshot](/cmpe283_2/images/7.png)
![Screenshot](/cmpe283_2/images/2.png)
4. After that all the steps need to be completed as the root user starting with running the command `make INSTALL_MOD_STRIP=1 modules_install` to install and package the modules
5. Check if the kvm module for the hypervisor is loaded using `lsmod | grep kvm`. 
6. If it already loaded, then get rid of it using the `rmmod kvm_intel` command
![Screenshot](/cmpe283_2/images/3.png)
7. Once that is done, use the `modprob kvm` command which will load th kvm if there are no errors in the code. 
8. Then, use the `lsmod | grep kvm` to see if the kvm is loaded

![Screenshot](/cmpe283_2/images/4.png)

9. Once all the steps are done, the machine needs to be rebooted.
10. After rebooting, install the virtual manager using `sudo apt install virt-manager`
11. In the Virtual Manager, add a test file to check the number of exits and the number of cycles spent in the exit
12. The other way to test is by installing cpuid package in the virtual manager by using `sudo apt install cpuid`
13. The output of CPUID manager before reboot is below:
![Screenshot](/cmpe283_2/images/6.png)

14. This is the final output:

![Screenshot](/cmpe283_2/images/5.png)

**Q3. Comment on the frequency of exits- does the number of exits increase at a stable rate? or are there more exits performed during certain VM operations? Approximately how many exits does a full VM boot entail?**

**Ans** The number of exits seem to increase at a constant rate. I tested the number of exits using my test code by calling the `cpuid` function inside my test C code by passing eax with the value of 0x4FFFFFFF. I noticed that the number of exits seem to increase about 4000-5000 exits everytime I checked using my test code. The most number of exits were observed during the VM bootup which easily crossed almost a million exits. The VM boot seemed to spent a lot of time in cycles and the exits were the largest compared to all other operations. Also, after doing some I/O operations like creating a file, the exits seem to increase significantly.

-----------------------------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------------------

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

-----------------------------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------------------


# CMPE283 : Virtualization

#### Assignment 4: Nested Paging vs. Shadow Paging
#### Name:  Puneet Tokhi
#### Professor: Michael Larkin

### Questions

**Q1. For each member in your team, provide 1 paragraph detailing what parts of the lab that member
implemented / researched. (You may skip this question if you are doing the lab by yourself).**

**Ans:** I did it by myself

#### Assignment instructions:
1. Run assignment 3 code and boot a test VM using that code. In my case, the test VM was also Ubuntu
2. Once the VM has booted, record total exit count information (total count for each type of exit handled by KVM). The CPUID leaf function used is `0x4FFFFFFD`
3. Shutdown the test (inner) VM.
4. Remove the ‘kvm-intel’ module from your running kernel using the command:
◦ `rmmod kvm-intel`
5. Find the module which is in the path: `/lib/modules/5.18.0-rc3+/kernel/arch/x86/kvm`
6. Reload the kvm-intel module with the parameter `ept=0`. This will disable nested paging and force the KVM to use shadow paging.
7. Boot the same test VM again, and capture the output again.

**Q2. Include a sample of your print of exit count output from dmesg from “with ept” and “without ept”.**

## `With EPT`
 ![Screenshot](cmpe283_4/images/3.png)
 
 ![Screenshot](cmpe283_4/images/4.png)
 
## `Without EPT`
 ![Screenshot](cmpe283_4/images/1.png)
 
 ![Screenshot](cmpe283_4/images/2.png)
 
 
 **Q3. What did you learn from the count of exits? Was the count what you expected? If not, why not?**
 
 **Ans:** There seemed to be a significant increase in the number of exits. The result was expected as there are large number of exits in shadow paging compared to nested paging due to increase in TLB flushes, CR3 exits and page faults. For example, in the case of exit type 0, which is exception or non-maskable interrupt, had only 11677 exits with EPT, but that number crossed over a million exits in the case of without ept. Also, I noticed that exit type 58(INVPCID) had 0 exit count with EPT, but without EPT that number was 28652. So, overall it was expected to see a large increase in exit types when the VM was run without EPT compared to running it with EPT.

 
 **Q4. What changed between the two runs (ept vs no-ept)?**
 
  **Ans:** A big change that I noticed was in the performance of the VM when it was running on no-ept. When no-ept was enabled, the VM was running much slower compared to with ept enabled slow. Also, there was a slighlt lag and the performance seemed slow while executing basic instructions and opening up my browser. Moreover, the VM boot up time also seemed to increase with no-ept compared to ept enabled. Also, I noticed that there were two additional exits, which was exit type 14 (INVLPG) and exit type 58 (INVPCID). These two exits occurred because of shadow paging which involves a TLB flush and for that we use the INVLPG command. Overall, I observed that between the two runs, exits count increase significantly in the case of no-ept compared to ept-enabled and the performance was noticeably slower in shadow paging compared to nested paging which was also expected.



