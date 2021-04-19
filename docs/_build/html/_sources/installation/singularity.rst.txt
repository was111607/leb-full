.. _singularity-installation-label:

Singularity Installation
-------------------------
Firstly, the user needs to install the Singularity software using the following steps described in “https://sylabs.io/guides/3.5/user-guide/quick_start.html”.

Note: Some installations require the user to have sudo privileges. If you do not have any, please ask your system administrator to install them for you.

Install the Required Packages
=============================

* You need to use the following 2 commands to install the dependency packages:

  ::

    sudo apt-get update
    sudo apt-get install -y \
    build-essential \
    libssl-dev \
    uuid-dev \
    libgpgme11-dev \
    squashfs-tools \
    libseccomp-dev \
    wget \
    pkg-config \
    git \
    cryptsetup

Install Go
==========
* This is used by the Singularity container:

  ::

    export VERSION=1.13 OS=linux ARCH=amd64
    wget https://dl.google.com/go/go$VERSION.$OS-$ARCH.tar.gz
    sudo tar -C /usr/local -xzvf go$VERSION.$OS-$ARCH.tar.gz
    rm go$VERSION.$OS-$ARCH.tar.gz
    sudo ln -s /usr/local/go/bin/go /usr/local/bin/go

* You can test your download by typing:

  ::

    go help

Download Singularity From a Release
===================================

* The following commands are used to download the code:

  ::

    export VERSION=3.5.2 && wget  https://github.com/sylabs/singularity/releases/download/v${VERSION}/singularity-${VERSION}.tar.gz
    tar -xzf singularity-${VERSION}.tar.gz
    cd singularity

Compile the Singularity Source Code
====================================

* The code is compiled using the following command:

  ::

    ./mconfig
    make -C builddir
    sudo make -C builddir install

* You can test the installation by typing:

  ::

    singularity h
