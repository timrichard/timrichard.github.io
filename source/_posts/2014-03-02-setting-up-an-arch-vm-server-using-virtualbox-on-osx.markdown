---
layout: post
title: "Setting up an Arch VM server using VirtualBox on OSX"
date: 2014-03-02 19:26:03 +0000
comments: false
categories: [osx,virtualbox,linux,arch]
---

## Objective

I wrote this post as a reminder of configuration needed for a new Arch Linux guest VM, to be used as a server on a Mac OSX host. 

[Arch](https://www.archlinux.org/) is a Linux distribution that offers simplicity, flexibility, and a great package management system. Another benefit is the [Arch User Repository](https://aur.archlinux.org/) (AUR), which provides access to a vast collection of packages that can be compiled from source and then installed using the regular package manager. This makes Arch a great choice for a Linux server guest, as there's a great chance that you'll be able to find newer software releases that you want to try out.

As mentioned below, I've cherry-picked appropriate installation steps from the [definitive](https://wiki.archlinux.org/index.php/Beginners'_guide) installation guide, and also a sub-guide for Arch as a [VM guest](https://wiki.archlinux.org/index.php/VirtualBox#Arch_Linux_as_a_guest_in_a_Virtual_Machine). The objective is to establish an Arch VM that's useful as a server on your Mac as quickly and easily as possible.

This post brings together some useful configuration options. These include an AUR-friendly package helper, dual NICs to serve from the guest to your host, and more.
<!--more-->
## VM setup

Begin by downloading an Arch ISO from the official [download](https://www.archlinux.org/download/) site. You can do this using BitTorrent, or an HTTP mirror near to you. The ISO contains dual installation facilities for 32-bit architectures, and also 64-bit which will be appropriate for your Mac.

### Default settings

Create a new VM in VirtualBox. In this example, I've called mine "Arch64base", as I intend to clone other VM's from it in the future. The type should be "Linux", and the version should be "Arch Linux(64 bit)". 

You can accept the default for memory size. 

On the next screen, accept the default of "Create a virtual hard drive now", and press the 'Create' button. 

In the next screen, you can leave the default of "VDI", and press the Continue button. 

Next, leave the default of "Dynamically allocated", and press the Continue button. 

### Linux guest HD size

The next screen allows you to choose a size for the new HD. In this case, we're going to alter the suggested default, and go for a size of 20GB. Note that this does not mean that 20GB will be allocated straight away on the Host hard drive. As we previously selected the "Dynamically allocated" option, the virtual hard drive will start out at a much smaller size and grow on demand up to the maximum that we specified.

So here, drag the slider until *about* 20GB is selected.

![Selecting a different HD size](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/HDsize.png)

This will allow a decently sized root partition for the packages you might want to install (Python, Ruby, Java, NodeJS, Go, etc) as well as room on /home for your project source files.

Now go ahead and press the Create button. 

You should now have a suitable VM to start the installation process.

We need to make some alterations to the default VM configuration before booting into the Arch Linux installer.

The new VM should be already highlighted in VirtualBox. In this example, mine is called Arch64Base.

![New VM created](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/NewVMcreated.png)

Now press the **Settings** button at the top of the window to make those alterations.

### CPUs

Choose the System button, and select the Processor tab.
You can drag the slider until "4 CPUs" is selected.

![2 CPUs](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/4CPUs.png)

>The plan here is to have more than one CPU core available, so that it's possible to test any software we try that's capable of scaling across more than one core.

### Selecting the ISO to install from

Now, choose the Storage category at the top. We need to boot from the Arch Linux ISO to start the install process. Click on the DVD icon next to "Empty", under "Controller: IDE". This should reveal attributes to the right. Click on the DVD icon to the right of the dialog so that you can select the ISO to install from. Select the "Choose..." option, so that you can navigate to the Arch ISO image that you previously saved to your Mac.

![Choose ISO image](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/vboxiso.png)

Navigate to the ISO image that you downloaded, and select it.

>In my case it lives in my *Downloads* directory, and is called *archlinux-2013.06.01-dual.iso*.

### Adding a second network adapter

Now click on the Network category at the top of the dialog. You should see Adapter 1 already selected. It is enabled, and is set to work in NAT mode. This basically means that it will piggyback on the network connection your Mac already has, and gain connectivity that way.

From here, select "Adapter 2" and then press "Enable Network Adapter". In the "Attached To" section, select "Host-only Adapter". Leave the name (vboxnet0) as default. 

>The plan here is to establish a second network adapter in our Linux guest, that is only has a connection to our host Mac. We can use it as a private testing server, without exposing it to the outside world.  The Linux VM can serve content to our Mac host via this second interface, but can still use its first (NAT) interface when it needs to access the Internet (for system updates, installing new packages, etc).

Your screen should now look like this :

![Second Network adapter](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/hostAdapter.png)

### <a name="SetupSharedFolders"></a>Shared folders

We should now tell VirtualBox how Shared Folders should be set up.

>This is a useful feature that lets the Linux Guest and Mac Host share files. You will be able to save files to a directory on your Mac, and they will appear in your Linux guest in a special directory. And vice versa. 

Using Finder, create a directory structure to hold the shared files.
Under your home directory, you might create a directory called "vmshare", and under that, a name suitable for the VM. In this case, I have created *vmshare/archbox*. We'll need to tell VirtualBox to map the shared folder to the directory you created shortly.

You should have a new directory similar to this one :

![New directory in Finder](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/newVMshareFolder.png)

Now select the Shared Folders button on the top. Click on the Add Shared Folder button, shown next to the pointer here :

![Add a share folder](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/AddSharedFolder.png)

You now need to specify the directory that we just created. Press the dropdown button to the right of "Folder Path", and navigate to the directory just created (in my example it was "vmshare/archbox").

In this case, it has automatically derived the name 'archbox' that we can use as a symbol for this share.

Be sure to tick the "Auto-mount" option, and press OK to dismiss this option box.

![Selecting a VM share](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/SelectShare.png)

Now you can press 'OK' to dismiss the main Settings dialog box. The VM is all configured.

## Arch Linux guest installation

### Boot the installer

Now press the Start button at the top to launch our newly created VM.

You should see the Arch install screen as shown below. If you don't, please review the section above about selecting the installation ISO.

![Arch booted into install](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/archInstallScreen.png)

Go ahead and press Enter to boot into x86_64 mode. This is the 64bit variant that's appropriate for your Mac.

After the install image has finished booting, you will be left with root access to begin the install process :

![Arch installer root access](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/rootInstall.png)

>From this point, we can fast-track through the Arch installation process. This is based on the assumption that you are installing on a Mac OSX host computer that has functional Internet connectivity. Arch provides a [definitive](https://wiki.archlinux.org/index.php/Beginners'_guide) installation guide, and a sub-guide for Arch as a [VM guest](https://wiki.archlinux.org/index.php/VirtualBox#Arch_Linux_as_a_guest_in_a_Virtual_Machine). However, I'll cherry-pick steps for this particular use case.

### Locale setup for the installation process

The Arch installer defaults to US locale for keyboard settings.
I'm in the UK, so I'll be executing the following :

    loadkeys uk
    
This step should give you the correct keyboard map during the installation.

>If you need to change locale too, please refer to [this](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2#Officially_assigned_code_elements) map and enter your equivalent in lowercase.

### Preparing the storage drive

>We are going to use the GPT (GUID Partition Table) and the Syslinux boot loader.

Use cgdisk to create the storage partitions :

    cgdisk /dev/sda

You may see a warning about your partitions. If you do, press Enter to skip past it.

You should now see this cgdisk configuration screen :

![cgdisk partition tool](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/cgdiskSetup.png)

Now press '**n**' to define the first partition. When prompted for the first sector, press Enter to accept the default. For the size in sectors, enter '**8G**'. 

>Note that you enter just '8G', and not '8GB'.

On the next prompt, press Enter to accept filesystem code 8300. The Ext4 filesystem is fine. Now press Enter to accept the blank partition name.

You should see the first partition defined as follows :

![First partition defined](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/partitionOneDefined.png)

>Partition number #1 is now defined, and it will occupy 8GB of space.
This will be our Root partition. We must now define the second partition.

Press the **down arrow** key twice, and make sure that the "free space" item is selected. Now press '**n**' to create a new partition in the free space. Press Enter to accept the "first sector" default. Press enter to accept the "size in sectors" default. Press Enter again to accept the default type 8300 filesystem. Finally, press Enter for a fourth time to accept a blank partition name. 

>You should now have two partitions defined. The first should be 8GB, and the second 12GB.

![Finished partitions](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/createdPartitions.png)

>Don't worry if your second partition has a slightly different size. We were shooting for approximately 20GB in total.

Now press **w** to write the changes. When prompted, you can enter **yes** to confirm. Now press **q** to quit cgdisk.

### Create the filesystems

Now, execute this to create the filesystem for the root partition :

    mkfs.ext4 /dev/sda1

And execute this to create the filesystem for the home partition :

    mkfs.ext4 /dev/sda2

### Mount the filesystems, to write during installation

Mount the root partition as /mnt :

    mount /dev/sda1 /mnt

Create a directory for the home partition, and mount as /mnt/home :

    mkdir /mnt/home
    mount /dev/sda2 /mnt/home

### Install the base system

At this point, we can install the base Arch Linux system :

    pacstrap -i /mnt base

Press Enter to accept the default of all, and Enter again to proceed with the installation. This may take a little while.

### Generate the File System Table

We now need to generate the master file system table (fstab), to reflect the state of our guest Arch VM.

    genfstab -U -p /mnt >> /mnt/etc/fstab

### Configure the base system

Now we'll switch into the newly installed system using chroot.

    arch-chroot /mnt /bin/bash

### Set the locale

At this point, we need to edit */etc/locale.gen* in order to define our preferred locale. You'll need to use your favourite editor to remove the comment character ('#') from the line you want to enable.

>If you're unfamiliar with Unix text editors, then *nano* would be a good choice here. You can find further guidance from [Nano documentation](http://mintaka.sdsu.edu/reu/nano.html). You also have *vi* available at this point.

    nano /etc/locale.gen

>It's best to uncomment the line for your locale that specifies UTF-8.
In my case, I'm going to remove the comment (#) from *en_GB.UTF-8*

Please make a note of the locale you selected, as we'll need it again shortly.

Now you can generate the system locale :

    locale-gen
    
Now we must create */etc/locale.conf*

Execute the following, but substitute the locale you made a note of earlier. This is mine, but substitute your locale if it's different :

    echo LANG=en_GB.UTF-8 > /etc/locale.conf

Export an environment variable, but again substitute your locale if different :

    export LANG=en_GB.UTF-8


### Set your hardware clock

Set the hardware clock to use UTC :

    hwclock --systohc --utc

### Choose a hostname

It's a good idea to choose a hostname for your new VM server, otherwise it will just default to "localhost". I've called this one "archbox". Choose something similar, and set it permanently by executing :

    echo yourhostname > /etc/hostname

### Set up the network interfaces

First, please execute :

    ip link
    
The output shows the details of the network interfaces available in the VM.

I've included a screenshot of mine below. The two interface names highlighted are important. The first one defines the NAT interface that will supply our VM with internet connectivity. The second is the host-only adapter that will serve your Mac OSX host from the guest. I've highlighted the interface IDs in my screenshot below. Please make a note of the ones on your system, as we'll need those names shortly.

![Two network interfaces](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/2networkInterfaces.png)

> I've made a note of enp0s3, enp0s8. Please make a note of yours if they are different.

Let's move to the netctl directory

    cd /etc/netctl
    
We'll copy over two network profiles from the */examples* directory, which we can use as templates for configuring our two network interfaces.

    cp examples/ethernet-dhcp .
    cp examples/ethernet-static .
    
We now have two suitable netctl profiles to edit.

Edit ethernet-dhcp, and replace 'eth0' with the **first** network ID you made a note of.

    nano ethernet-dhcp

In my case, the ethernet-dhcp file now looks like the screenshot below. You can see the network name highlighted.

![First network interface - NAT](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/ethDhcpNat.png)

Now we need to edit the second configuration file.

    nano ethernet-static

In this case, you will need to replace 'eth0' with the **second** network ID that you made a note of. In my case, that will be **enp0s8**.

Also, change the Address line to show **Address=('192.168.56.56/24')**

>192.168.56.56 is the static IP that your Arch Linux VM will always respond on.

Then, change the DNS line to show **DNS=('8.8.8.8')**

Also, comment out the Gateway= line. Push a hash/pound character before it.

My ethernet-static file now looks like this (changes highlighted) :

![Second network interface - Host only](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/ethStaticHost.png)


Now, we just need to enable the two new network profiles :

    netctl enable ethernet-dhcp
    netctl enable ethernet-static

Each time, don't worry about the message that says "Running in chroot, ignoring request".

### Set up a root password

You're configuring the Arch system as the root user, and need to supply a suitable password for future use. Execute this :

    passwd
    
You'll be prompted to enter a suitable password, and once again to confirm.

### Set up the bootloader

We'll be using Syslinux here. As we used GPT for the partition earlier, execute :

    pacman -S gptfdisk

Install and configure Syslinux :

    pacman -S syslinux
    syslinux-install_update -i -a -m

We're almost set for Syslinux to boot our new system, but some of the installed defaults aren't quite right. Our root partition is on /dev/sda1, and the Syslinux configuration needs to reflect that. Edit /boot/syslinux/syslinux.cfg

    nano /boot/syslinux/syslinux.cfg
    

You need to locate the two Arch labels (arch, archfallback) and switch their partition assignments from *sda3* to *sda1*. 

I've highlighted where I've made the switch on my VM :

![Syslinux configuration](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/syslinuxconf.png)

### Prepare for reboot

Exit the chroot environment :

    exit
    
Unmount the partitions under /mnt :

    umount -R /mnt
    
Remove the ISO image from the VirtualBox drive.
To do this, switch back to the VirtualBox Manager. Make sure your VM is highlighted, and then click on the Settings button at the top.
Under the Storage tab, highlight the archlinux ISO that is under "Controller: IDE". Click the DVD icon to the right, and choose "Remove disk from virtual drive" :

![Remove disk from virtual drive](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/removeDiskVirtualDrive.png)

Now switch back to your Arch VM, and instruct it to reboot :

    reboot

### Boot into your new VM

When the VM has finished booting, you need to log in.
Do this as root, with the root password you chose earlier.

### Install essential packages

As this will be a developer's VM, there are base build tools that will be useful to you.

Execute the following :

    pacman -S base-devel

![Installing base-devel](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/pacmanBaseDevel.png)

Press Enter **twice** to accept defaults, and install.

### Add yourself as a user

Execute the following, and insert your name instead of 'yourname' :

    useradd -m -s /bin/bash yourname

Now add a password for that user account you created :

    passwd yourname
    

### Setting up sudo

It's now time to configure sudo, which is a utility to let you run commands as another user (most typically root). You'll be using this fairly regularly, so we'll make it easy.

Execute the following :

    usermod -g wheel yourname

... where of course you substitute your name for 'your name'.

This will add your regular user account (that you created in the steps above) to the group 'wheel'. We'll tell Sudo via configuration that anyone in that group can use sudo to execute root commands without having to enter a password.

We need to alter the Sudo configuration now. It isn't meant to be modified manually, so we'll use a tool called *visudo* to accomplish this.

>If you're familiar with *vi*, and have been using it through these steps, then you'll be able to use *visudo* directly. It will open up the Sudo configuration in *vi* directly. If you've been using *nano* instead, then you can execute a slightly different command to edit the configuration file using *nano*.

To edit the Sudo configuration in nano, execute :

    EDITOR=nano visudo
    
**Alternatively**, execute this instead to edit with *vi* :

    visudo
    
Locate this line, and remove the leading **# and space** before "%wheel" :

![Sudo config for wheel](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/visudo.png)

So now you have this :

![Correct sudo configuration](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/visudo_ok.png)

That's all complete. From this point, you'll be able to use sudo without any password from your regular user account.

### Setting up yaourt, a meta package manager

Yaourt is a wrapper around the system's package manager - Pacman. It's a really great way of opening up the AUR to your Arch server, which will give you access to newer and lesser known packages. Yaourt makes it easy to search the AUR, and will automatically install any packages you select. This might involve a prebuilt binary, or the tool may checkout source from a Git repo and compile it on your machine. You don't have to worry, as Yaourt will take care of the process, generating a package that can be installed and later removed just like any other.

Yaourt isn't available through the regular Arch repositories, so it can't be installed directly through Pacman. The easiest way to get it installed on your system is to include the Yaourt project repo to your list of repos, and let Pacman find it that way.

Using either *nano* or *vi*, please edit */etc/pacman.conf*

At the very end of the file, add these three lines :

    [archlinuxfr]
    SigLevel = Never
    Server = http://repo.archlinux.fr/$arch

Save and close the file.

Now execute this to install yaourt :

    pacman -Sy yaourt
    
Press **y** when prompted to proceed with the installation.

You should now have yaourt installed.

>Please note that it's best practice to run *yaourt* under your regular user account, rather than the root account that you're currently using for system configuration.

As an example, we could use yaourt to search for CasperJS (which is a scripting environment for the headless PhantomJS WebKit browser) :

![Yaourt search for CasperJS](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/yaourt1.png)

Alternatively, we could search for a Load Balancer and receive a number of choices (including HAProxy) :

![Yaourt search for a load balancer](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/yaourt2.png)

## VirtualBox guest additions

Now we can install the VirtualBox Guest Additions. This package consists of drivers and utilities to optimise our Arch installation for life within a VirtualBox VM. Many of the [features](https://www.virtualbox.org/manual/ch04.html) aren't really applicable to our console-driven server VM. However, we get benefits like Shared Folders, which will make it easy to swap files between our Mac host and the Linux VM guest.

>Many sources refer to the Guest Additions disk image that you can mount as an installation source. Fortunately, we can use a repository to automatically install the additions for us. Another bonus is that the Guest Additions package receives updates, so our upgrade path is pretty seamless when Arch wants to install a new kernel via system updates.

Let's start by installing the Guest Additions package.

    pacman -S virtualbox-guest-utils

A large number of packages will be marked for download.
Enter **y**, and continue with the installation.

We can test out the new kernel modules. Execute this :

    modprobe -a vboxguest vboxsf vboxvideo

Several kernel modules will have been installed. We need to make sure they are available each time we boot the VM.

Create */etc/modules-load.d/virtualbox.conf* using your preferred editor.

In the new file, add these three lines :

    vboxguest
    vboxsf
    vboxvideo

Make sure that your user account is part of the group that grants access to Guest Additions features. Execute this, swapping 'yourname' for the name of the user account you created for yourself :

    gpasswd -a yourname vboxsf

Enable the Guest Additions services by executing this :

    VBoxClient-all

...and start the services automatically on boot. Execute this :

    systemctl enable vboxservice

Now let's test those Guest Additions kernel modules by rebooting.

Execute this :

    reboot

At this point, it's a good time to test out that user account that you created earlier.

When the VM wants you to login, enter the username and password that you chose (not the Root account we have been using up until now).

Make sure your user account can reach the automounted shared folder directory. Execute the following :

    sudo chmod o+rx /media

>I'm not entirely sure why this is necessary, but it seems that the /media directory doesn't have the correct permissions to allow non-privileged accounts to reach the mounted directories beneath. I think this may be a bug in the current Arch/VirtualBox configuration.

Now we can test out the shared folder. 

>You'll now need that hostname that you set earlier. Luckily, it's easy to spot. It should be right there in your command prompt. The VirtualBox service automatically mounts the shared folder under */media*, using the hostname you chose prepended with "sf_". So in my case here, the shared folder is called */media/sf_archbox*

Edit a new file in your shared folder using *nano* or *vi*, substituting your folder name for 'your folder' :

    nano /media/sf_yourfolder/testing.txt

Add some sample text. You should be able to navigate to that file in the Mac directory that you [chose earlier](#SetupSharedFolders), and see the text that you just entered.

## Testing with NodeJS

The VM is now ready to be used as a very versatile server.

Let's test the setup by creating a simple NodeJS server to respond to web requests. In fact, it's the short example code given on the NodeJS homepage.

First, let's install NodeJS. It's a package in the Community repository, so we don't have to find and build it using Yaourt :

    sudo pacman -S nodejs

Switch to your home directory, and create a file called *app.js*. It should contain these lines :

    var http = require('http');
    http.createServer(function (req, res) {
      res.writeHead(200, {'Content-Type': 'text/plain'});
      res.end('Hello from our Arch guest!\n');
    }).listen(8080, '192.168.56.56');
    console.log('Server running at http://192.168.56.56:8080/');
    
>The important thing to note in this code fragment is that we've bound to the static IP address that the host can reach us on (192.168.56.56), rather than 'localhost' or '127.0.0.1'.

When you've saved this file, you can run it by executing :

    node app.js
    
The server should respond with :

    Server running at http://192.168.56.56:8080/

Try accessing that address in a browser on your Mac, to make sure it is all working :

![Host browser accessing Guest VM](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-02/testingServerOnHost.png)

## Wrap up

I hope the VM is useful, and you find a lot of great packages to experiment with.

It's a good idea to update your Arch system fairly regularly. You can achieve this through the Yaourt wrapper :

    yaourt -Syu --aur
    
... which has the benefit of updating any packages that you've acquired through the AUR at the same time. It's not unusual for changes to require some maintenance on your part. There's normally plenty of notice on the [home page](https://www.archlinux.org/) when this happens. Also, the benefit of a VM approach is that you can set snapshots whenever you want, and revert to them if anything goes wrong with your installation.
