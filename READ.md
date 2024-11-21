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



![image](https://github.com/user-attachments/assets/8a8ca22c-d028-491d-af5e-4a0065c01803)



