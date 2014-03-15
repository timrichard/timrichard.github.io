---
layout: post
title: "Node &amp; NPM version management, and sudo free global packages"
date: 2014-03-15 16:19:28 +0000
comments: false
categories: [nodejs,npm]
---
I wrote this post as a reminder of two good configuration  ideas when using NodeJS and NPM.

##Using NVM to manage Node versions

[Node Version Manager](https://github.com/creationix/nvm) is a great script that makes it easy to manage Node/NPM versions. NVM prepends to your path, which results in you executing the version of NodeJS/NPM that NVM is currently managing. 

This can be useful if you are using packages from NPM that don't work yet with certain versions of NodeJS. You could perhaps lock down your version of NodeJS and the packages you depend on, and then later increment in a controlled way in  a testing environment to make sure everything still works.

### Prerequisites

If you're an OSX user, the [documentation](https://github.com/creationix/nvm) suggests that you need XCode installed to make use of the C++ compiler. 

You can download the command line tools from the XCode GUI, or through the following line :

    xcode-select --installed

Also, Git needs to be installed on your system.


### Installation

To paraphrase the [homepage](https://github.com/creationix/nvm), installation is very easy :

    curl https://raw.github.com/creationix/nvm/master/install.sh | sh

This installation will append a line to your shell profile to execute nvm.sh, which will now exist in your *.nvm* directory. 

>Although the documentation mentions this install method will append to your .bash_profile and .profile dotfiles, the source code of the installer suggests it will also append the line to *.zshrc* if you're a *zsh* user.

### Features

I'll link to the [homepage](https://github.com/creationix/nvm) rather than simply copying the usage documentation here, but NVM offers a number of really useful options.

You can execute **nvm ls-remote** to see which versions of NodeJS are available to you. **nvm install** allows you to download a particular version of NodeJS to be stored locally for future use. **nvm ls** will show you the versions that you have installed locally, and you can **nvm use** any of them whenever you want. You have options to make a particular version your default for your environment, and can also script project-level configuration dotfiles to specify particular NodeJS versions for different projects.

Executing *which* on my local installation, we can see that the NVM versions are in use :

![NVM controlling NodeJS and NPM versions](https://googledrive.com/host/0B_Aq_PyDmRw1OVhUMHpybGg5aGc/Blog/2014-03-15/whichNodeNPM.png)

NVM offers flexibility over the official NodeJS installer, which operates system-wide. The per-user profile and path approach makes it a non-destructive way to swap versions easily.

##Global NPM packages without Sudo installation

Some NPM packages recommend global installation (using *npm -g*. This allows packages to executed as commands from any location on your system. For instance, *Express* is used in this way to generate Web framework scaffolding into any location you choose. 

Executing an install with the global option often fails because the non-privileged user doesn't have write access to the destination (such as */usr/local*). One approach is to re-execute the install command using *sudo*. NPM even suggests this in the errors that are thrown. 

Some people reject this as a security concern, as you would be granting root access to any scripts coming down from the package manager. Another [approach](http://howtonode.org/introduction-to-npm) would be to make your user account the owner of the */usr/local* and any subdirectories. This may raise other concerns, as your OS might be expecting that directory to remain under root ownership.

A number of solutions are posted [here](https://gist.github.com/isaacs/579814). I like the last one, as it takes a simple non-destructive approach similar to the NVM installation earlier in this post.

Edit your *~/.npmrc*, and make sure this line is present :

    prefix = ~/local

Also make sure that *~/local/bin* is on your path, by including this line in your shell profile dotfile :

    export PATH=$HOME/local/bin:$PATH

At this point you can *source* your changed file, or perhaps restart the terminal session for the changes to take effect.

You should now be able to install packages globally without sudo, for example :

    npm install -g express
    
