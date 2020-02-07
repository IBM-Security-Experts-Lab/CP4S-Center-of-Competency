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

1. Install KVM packages: [Reference: Red Hat KVM Installation Link](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/virtualization/sect-virtualization-installing_the_virtualization_packages-installing_kvm_packages_on_an_existing_red_hat_enterprise_linux_system)
 - `sudo yum install  qemu-kvm  qemu-img  virt-manager libvirt` libvirt-python libvirt-client  virt-install virt-viewer  bridge-utils
 - Reboot the system
2. Start the libvirtd service
 - `sudo systemctl enable libvirtd`
 - `sudo systemctl start libvirtd`
3. Verify if KVM installation is ok.
 - `sudo lsmod | grep -i kvm`
 ![image](/text-mode-images/virf-kvm-install.png)

