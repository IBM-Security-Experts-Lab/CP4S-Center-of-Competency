# CP4S-Center-of-Competency

## Text-Mode Installation Guide

### Introduction

Text mode installation offers an interactive, non-graphical interface for installing Red Hat Enterprise Linux. This can be useful on systems with no graphical capabilities; however, you should always consider the available alternatives before starting a text-based installation. Text mode is limited in the amount of choices you can make during the installation. 

### Pre-requistes

 1. Ensure that a VNC server is running on the host machine, add your user-id / password to the server, and verify that you can log into the machine.
 2. In your hose server download the RHEL 7.7 iso file in the /tmp folder.

- /tmp folder can be accessed qemu-user
- We have downloaded this iso file: /tmp/RHEL-7.7-20190723.1-Server-x86_64-dvd1.iso

### Steps
 1. [Install Virtual Machines](#Install-Virtual-Machines-(VM))
 2. [Configure Virtual Machines](#Configure-Virtual-Machine-using-Text-Mode-Interface)

#### Install Virtual Machines (VM)

We need 4 VMs: 1 master and 3 worker nodes.  Here are the steps for those.

 1. Install KVM packages: [Reference: Red Hat KVM Installation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/virtualization/sect-virtualization-installing_the_virtualization_packages-installing_kvm_packages_on_an_existing_red_hat_enterprise_linux_system)
    - `sudo yum install  qemu-kvm  qemu-img  virt-manager libvirt libvirt-python libvirt-client  virt-install virt-viewer  bridge-utils`
    - Reboot the system
 2. Start the libvirtd service
    - `sudo systemctl enable libvirtd`
    - `sudo systemctl start libvirtd`
 3. Verify if KVM installation is ok.
    - `sudo lsmod | grep -i kvm`

 ![image](/text-mode-images/virf-kvm-install.png)

 5. Create Bridge Interface 
 
 6. Start Intalling Master Node using the following command:

```
  sudo virt-install /
    --virt-type=kvm --name=master1 /
    --memory=2048,maxmemory=4096 -- vcpus=2/
    --debug --wait=5 /
    --os-variant=rhel7.7 /
    -l /tmp/RHEL-7.7-20190723.1-Server-x86_64-dvd1.iso /
    --network=bridge=br0,model=virtio /
    --nographics -- extra-args="console=tty0 console=ttyS0,115200n8" /
    --disk path=/var/lib/libvirt/images/rhel7VMmstr.qcow2,size=30,bus=virtio,format=qcow2
```

#### Configure Virtual Machine using Text Mode Interface

- If the figure below does not appear in CLI after running the command above you cannot proceed.
- The values entered in this section follow the [Quick Installation Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/installation_guide/chap-simple-install), but because this is a text mode installation the amount of choices you can make are limited.  

    ![image2](/text-mode-images/AnacondaCFG1.png)

**Figure 1.1. Text Mode Installation**

Installation in text mode follows a pattern similar to the graphical installation: There is no single fixed progression; you can configure many settings in any order you want using the main status screen. Screens which have already been configured, either automatically or by you, are marked as `[x]`, and screens which require your attention before the installation can begin are marked with `[!]`. Available commands are displayed below the list of available options.  **Note** When related background tasks are being run, certain menu items can be temporarily unavailable or display the `Processing...` label. To refresh to the current status of text menu items, use the **r** option at the text mode prompt.

1. Language settings
   - Unable to change from English
2. Time settings
   - Set time to host machines time-zone
3. Installation source
   - Even though the .iso was provide in CML, you still have to go through the steps. enter `3` and continue through the Installation source steps `c`. 
4. Software selection
   - by default text-mode will preform a minimal install

    ![image3](/text-mode-images/softwareSelection1.png)

   - enter `c` to continue through configuration

5. Installation Destination

    ![image3](/text-mode-images/instalationDest1.png)

   - The following pages are for autopartitioning options, enter `c` to use default settings
6. Kdump (already set)
7. Network Configuration
   - Set up hostname and IPv4 specific to Virtual Machine

   ![image4](/text-mode-images/networkFinal.png)

   - Make sure `Connect automatically after reboot` and `Apply configuration in installer` or selected

8. Root password
   - Set password for root directory.
9. User creation
   - If root password is provided creating a user is optional


    ![image5](/text-mode-images/finalScreen.png)

   - If all `[!]` are not present - enter `b` to begin installation.  After installation is complete the following is prompted: 

    ![image6](/text-mode-images/afterConfig.png)

   - From here you can log in to your virtual machine using your `root` password created in `step 8`

   - To verify that your new virtual machine is running, enter `virsh list` from you host machine.  The following image should appear.  

   ![image7](/text-mode-images/virshlist.png)

   **NOTE** The `id` should be one in your environment 

   - To verify that the network configurations are correct enter `ping (IP address created in step 7)` or enter `ssh -l root (IP address created in step 7)` use root password from step 8 to log in. 

#### Creating Worker VM's

We will be creating 3 worker virtual machines.  To do so, we are going to clone the `master1` virtual machine but change the hostname and IP address.

 1. In order to clone `master1` it must be paused or shutdown first

`virsh shutdown master`

 2. Clone `master1` into worker(i) i being the number of the worker.

`virt-clone --original master1 --name worker1 --auto-clone`

 3. To change the hostname and IP of the worker VM, ssh into it.  To do so, use the same ssh call used in the section above 
    - Because our `master1` virtual machine is shut down, the cloned virtual machine is still using the same IP address.  

`ssh -l root (IP address of master1)`

 4. While in the cloned virtual machine change the host name of the virtual machine

`hostnamectl set-hostname worker1`

 - To verify the change enter `hostnamectl status` the new hostname should be present at the top.  

5. To change the IP address modify the `/etc/sysconfig/network-scripts/ifcfg-eth0` file

`vi /etc/sysconfig/network-scripts/ifcfg-eth0`

   - While in the file: 
     - to edit file go into insert mode, press `i`
     - change the `IPADDR` value to the new IP address, example: `IPADDR "1.1.1.2"`
     - enter `:wq` to save and exit

6. To finalize new worker VM enter

`systemctl restart network` 

7. To verify the new hostname and IP was created for `worker1` enter `virsh list` in host machine, the new VM should be present.  


8. Repeat steps 2-7 for remaining VM's 

9. After creating worker VM's enter `virsh list` to verify their creation

![image8](/text-mode-images/virshfinal.png)
   - To see `master` enter `virsh start master`
   - **Note** the `id's` will not be the same as image above