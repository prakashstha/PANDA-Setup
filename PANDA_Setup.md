# PANDA Setup

**Goal:** My goal is to setup and PANDA into (relatively) clean install of Ubuntu 18.04.5 LTS (Bionic Beaver) on my Lenovo Thinkpad W530 (Intel Core i7, 16GB RAM) so that I can play around with its features and plugins.

## Install PANDA
Install and build instructions are available in the [install_ubuntu.sh](https://github.com/panda-re/panda/blob/master/panda/scripts/install_ubuntu.sh) . Simply execute it in the terminal. 

```console
$ chmod +x install_ubuntu.sh # make the shell script executable.
$ ./install_ubuntu.sh
```
With a successful build and installation, it will prompt the following message in your terminal.
```console 
$ PANDA is built and ready to use in panda/build/[arch]-softmmu/panda-system-[arch]
```

## Working Directory with Disk Image
I am creating a working directory to store files for my test. I will be using *Ubuntu 20.04.1 LTS (Focal Fossa)* in my VM (i.e., PANDA) for my test. It can be any other OS.

```console
$ mkdir test_20200923
$ cd test_20200923
```
Now, panda is going to need a HDD in a qcow2 format. 

```console
$ qemu-img create -f qcow2 ubuntu-20.qcow2 100G
```

Download an ISO image of Ubuntu 20.04
```Console
$ wget https://releases.ubuntu.com/20.04/ubuntu-20.04.1-desktop-amd64.iso
```

## Launch VM and Install an OS
Launch VM and Install Ubuntu 20.04 from its iso image. It will install the OS to qcow2 image.

```Console
sudo qemu-system-x86_64 \
  -m 8192 \
  -vga virtio \
  -show-cursor \
  -usb \
  -device usb-tablet \
  -cdrom ubuntu-18.04.5-desktop-amd64 \
  -enable-kvm \
  -drive file=ubuntu-20.qcow2,if=virtio \
  -monitor stdio
```
I would suggest to choose minimal installation (rather than full installation) for the test purpose. 

Once the OS has been installed, You can quit qemu and power-off the VM by issuing the `quit` command at the qemu prompt:

```console
(qemu) quit
```

Launch the VM and boot the OS (Ubuntu 20.04 in my case) from qcow2 image created earlier.

```Console
sudo -E env "PATH=$PATH" panda-system-x86_64 \
  -m 8192 \
  -vga virtio \
  -show-cursor \
  -usb \
  -device usb-tablet \
  -enable-kvm \
  -drive file=ubuntu-20.qcow2,if=virtio
  -monitor stdio
```




