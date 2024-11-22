# Work done 

**Saurabh:** Implemented the functionality for tracking KVM exits by modifying the exit handler in the kernel. Added logic to count and log the exit types during KVM operations.

**Dharmesh:** Configured the kernel to enable KVM support and set up the environment for kernel compilation. mpiled the modified kernel, tested it, and verified that the exit profiling worked and solved issues during the kernel build process.



# Steps to do 


## Step 1: Fork and Clone the Linux Kernel Repository
Fork the Repository:
Go to Torvalds' Linux repository and fork the repository to GitHub account. 

## Step 2: Clone the Repository:

## Step 3: Navigate to the cloned repository

## Step 4: Install Dependencies
Install Build Tools:
SSH into your outer VM  and run:


sudo apt-get update \
sudo apt-get install build-essential libncurses-dev bison flex libssl-dev libelf-dev bc

## Step 5: Configure the Kernel

To start Kernel configuraton, run 

make menuconfig

This will open the interactive menuconfig tool. \
Enable KVM Support:

Enable KVM: Navigate to General setup → Virtualization and enable Kernel-based Virtual Machine (KVM) support. \
Enable KVM for Intel/AMD Processors:

## Step 6: Save the Configuration

## Step 7: Modify the KVM Exit Handler


## Step 8: Modify code to handle exits
   - In the exit handler function, increment a counter each time an exit occurs. Log the exit type and count every 10,000 exits using `printk` for logging.
   Example modification in the handler:
   ```c
   static unsigned long exit_counters[256] = {0}; // Array for tracking exit types
   static unsigned long total_exits = 0;

   void handle_exit(int exit_type) {
       exit_counters[exit_type]++;
       total_exits++;
       if (total_exits % 10000 == 0) {
           printk(KERN_INFO "Total Exits: %lu\n", total_exits);
           for (int i = 0; i < 256; i++) {
               if (exit_counters[i] > 0) {
                   printk(KERN_INFO "Exit type %d: %lu\n", i, exit_counters[i]);
               }
           }
       }
   } 
   ```

## Step 9: Compile the kernel 

make -j$(nproc) bzImage \
make -j$(nproc) modules \
make -j$(nproc) modules_install \
make -j$(nproc) install

## Step 10: Install Kernel and Modules 

sudo make modules_install \
sudo make install


# Frequency of exits

The frequency of exits increased over time and at a stable rate during the run time of the vm. Howeveer a spike can be noticed when the operation involving I/O operations take place. The full VM boot entail approximately around 50,000 exits. The number of exits that happen during the boot time are much more than those occuring when the system is stable as during boot time, the system needs to initialise  and configure resources and hardware.

# Most Frequent exits
Some of the most frequent exits were I/O Instrucitn exits (KVM_EXIT_IO), the window interrupt exits and even the timer exits. 

# Least Frequent exits
The least frequent exit types were the CPUID exits( when guest vm requests features of virtualised processor) and the hypercalls.CPUID exits happen mainly during guest OS initialisation only. Triple faults are the most rare exit types as they appear only druing sever error in the the guest.







![image](https://github.com/user-attachments/assets/8a8ca22c-d028-491d-af5e-4a0065c01803)



