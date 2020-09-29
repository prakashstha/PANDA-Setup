- [Creating a Ubuntu Desktop 20.04 Virtual Machine with PANDA](#creating-a-ubuntu-desktop-2004-virtual-machine-with-panda)
  - [Install PANDA](#install-panda)
  - [Working Directory and Downloading Ubuntu ISO](#working-directory-and-downloading-ubuntu-iso)
  - [Launch VM and Install the Ubuntu OS](#launch-vm-and-install-the-ubuntu-os)
  - [Reboot without Ubuntu ISO attached](#reboot-without-ubuntu-iso-attached)
  - [Setting up an appropriate snapshot](#setting-up-an-appropriate-snapshot)

# Creating a Ubuntu Desktop 20.04 Virtual Machine with PANDA
**Goal:** My goal is to setup and PANDA into (relatively) clean install of Ubuntu 18.04.5 LTS (Bionic Beaver) on my Lenovo Thinkpad W530 (Intel Core i7, 16GB RAM) so that I can play around with its features and plugins.

## Install PANDA
Install and build instructions are available in the [install_ubuntu.sh](https://github.com/panda-re/panda/blob/master/panda/scripts/install_ubuntu.sh) . Simply execute it in the terminal. 

```Console
$ chmod +x install_ubuntu.sh # make the shell script executable.
$ ./install_ubuntu.sh
```
With a successful build and installation, it will prompt the following message in your terminal.

```Console 
$ PANDA is built and ready to use in panda/build/[arch]-softmmu/panda-system-[arch]
```

## Working Directory and Downloading Ubuntu ISO
I am creating a working directory to store files for my test. I will be using *Ubuntu 20.04.1 LTS (Focal Fossa)* in my VM (i.e., PANDA) for my test. It can be any other OS.

```Console
$ mkdir test_20200923
$ cd test_20200923
```

Download an ISO image of Ubuntu 20.04

```Console
$ wget https://releases.ubuntu.com/20.04/ubuntu-20.04.1-desktop-amd64.iso
```




## Launch VM and Install the Ubuntu OS

PANDA/QEMU is going to need a hard disk (HDD) in a qcow2 format. Run the following command to create 100GB HDD (adjust your needs) for the VM.

```Console
$ qemu-img create -f qcow2 ubuntu-20.qcow2 100G
```
Explanation
 - ```qemu-img```:  call the ```qemu-img``` program that manages images.
 -  ```create```: create a new HDD.
 - ```-f qcow2```: specify the format of HDD as qcow2.
 - ```ubuntu-20.qcow2```: the name of the HDD
 - ```100G```: size of the HDD

Now, you have a folder containing the Ubuntu ISO and HDD in which Ubuntu will be installed on.

Launch QEMU VM with Ubuntu ISO and HDD that we created in the previous step attached. It will install the OS to HDD. **NOTE**: Make sure that the virtualization (VT-x/AMD-V)in your BIOS setting is enabled otherwise, the installation and booting processing will be super slow.

```Console
sudo qemu-system-x86_64 \
  -m 8192 \
  -vga virtio \
  -show-cursor \
  -usb \
  -device usb-tablet \
  -cdrom ubuntu-18.04.5-desktop-amd64.iso \
  -enable-kvm \
  -drive file=ubuntu-20.qcow2,if=virtio \
  -monitor stdio
```
Explanation

 - ```qemu-system-x86_64```: launch qemu hypervisor with x86_64 architecture
 - ```-m 8192```: the memory size to give to VM
 - ```-vga virtio```:  set the video graphic as virtio
 - ```show-cursor```: show mouse cursor in a QEMU VM
 -  ```usb```: enable the usb driver (optional)
 - ```device usb-tablet``:
 - ```cdrom ubuntu-18.04.5-desktop-amd64.iso```: attach Ubuntu ISO image to cdrom. 

If everything goes well, with the above command you should be able to install Ubuntu. I would suggest to choose minimal installation (rather than full installation) for the test purpose. 


## Reboot without Ubuntu ISO attached

Once the OS has been installed, You can quit qemu and power-off the VM by issuing the `quit` command at the qemu prompt:

```Console
(qemu) quit
```

Run the following command to reboot without Ubuntu ISO attached

```Console
sudo -E env "PATH=$PATH" panda-system-x86_64 \
  -m $MEMORY \
  -vga virtio \
  -show-cursor \
  -usb \
  -device usb-tablet \
  -enable-kvm \
  -drive file=$IMG_PATH,if=virtio \
  -accel kvm \
  -cpu host \
  -monitor stdio
```

Ubuntu 20.04 should now be installed and it should run fast, or at least in a usable speed, on Lenovo running Ubuntu 18.04 since hardware acceleration was enabled by specifying ```-accel``` kvm option

## Setting up an appropriate snapshot

We can set up the VM to a state where we can easily execute our application from outside the VM box. 
To do so, launch the VM in PANDA and open a terminal inside the guest environment. 
We can then utilize this snapshot by interfacing with the terminal from outside the VM box by sending appropriate keystrokes. 

At this point, type ```savevm <some name>``` in the QEMU command line interface to save the current VM state. 


