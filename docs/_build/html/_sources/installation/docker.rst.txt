.. _docker-installation-label:

Docker installation
-------------------------
To set up local enterobase, a prerequisite is having docker installed on the host machine. 

Check if Docker is Installed
=============================
* Run the following command to verify whether docker is installed on the host system:

  ::

    docker -v

Install Docker
==============
* The installation steps are found here "https://docs.docker.com/engine/install/ubuntu/".

* The installation process is specific to the distro being used, and the official documentation is comprehensive on the installation process.

* NOTE: If you are using CentOS 8, or Fedora 32 and above, Docker is no longer officially supported but still works. Instructions on how to install Docker on these OS are provided below. Please note both of these will require restarting the server. 

  .. list-table:: Officially Supported Distros By Docker
     :widths: 25, 10, 10, 10
     :header-rows: 1

     * - Operating System 
       - x86_64 / amd64
       - ARM
       - ARM64 / AARCH64
     * - CentOS 7
       - Yes
       -
       - Yes
     * - Debian 9/10 
       - Yes
       - Yes
       - Yes
     * - Fedora 30/31
       - Yes
       -
       - Yes
     * - Raspbian 
       -
       - Yes
       - Yes
     * - Ubuntu 16.04/18.04/20.04
       - Yes
       - Yes
       - 

* NOTE: that the installation process, and Docker itself requires the user to have sudo privileges.  
* If you do not have sudo privileges please contact your system administrator to install and setup the container.


Example for Ubuntu
==================
* There are two approaches that can be taken that are outline below.

**Approach 1**

* The following command can be used to install Docker in one command:

  ::

    snap install docker     # version 19.03.11

**Approach 2**

* These are the steps outlined in the official docs to install Docker on the VM:

1. Update the *apt* package index and install packages to allow *apt* to use a repository over HTTPS:

  :: 
    
	sudo apt-get update

	sudo apt-get install \
	  apt-transport-https \
	  ca-certificates \
	  curl \
	  gnupg-agent \
	  software-properties-common

2. Add Docker's official GPG key:

  ::
    
	curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

3. Use the following command to set up the stable repository for x86_64/amd64 architectures:

  ::
    
	sudo add-apt-repository \
	"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
	$(lsb_release -cs) \
	stable"

4. Install the Docker Engine:
  
  ::

    sudo apt-get install docker-ce docker-ce-cli containerd.io

5. Verify its installation by running the hello-world image:
 
  ::
  
    sudo docker run hello-world

Example for CentOS 8
====================

* Make sure the system is update by running the following command, then rebooting if necessary:

  ::

    sudo dnf update -y ; reboot

1. Enable docker-ce repository:

  ::
    
	dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo

2. Verify there is a version of docker-ce available to download:

  ::
    
	dnf list docker-ce

	Docker CE Stable - x86_64 1.7 kB/s | 3.8 kB 00:02
	Available Packages
	docker-ce.x86_64       3:19.03.13-3.el8            docker-ce-stable

3. Install docker-ce

  ::
   
   dnf install docker-ce --nobest -y

4. Start and enable the service:

  ::
    
	systemctl start docker
	systemctl enable docker

5. Verify its installation:
  
  ::
    
    docker --version

Example for Fedora 31, 32, and 33
==================================

* This will involve installing the open-source version of Docker, Moby, which is better maintained for these verions of Fedora.

1. Uninstall old versions of docker
  
  ::
    
	sudo dnf remove docker-*
	sudo dnf config-manager --disable docker-*

2. Enable old CGroups as Docker does not currently support CGroupsV2:
  
  ::

    sudo grubby --update-kernel=ALL --args="systemd.unified_cgroup_hierarchy=0"

3. Setup firewall: 
  
  ::
  
    sudo firewall-cmd --permanent --zone=trusted --add-interface=docker0
    sudo firewall-cmd --permanent --zone=FedoraWorkstation --add-masquerade

4. Install Moby:

  ::

    sudo dnf install moby-engine docker-compose
    sudo systemctl enable docker

5. Restart server:
  
  ::

    sudo reboot

6. Verify its installation:

  ::
    
	docker --version
