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

1.  