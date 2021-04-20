Installation Requirements
--------------------------

Prerequisites
==============

* A server to host Local EnteroBase that meets the minimum system requirements below
* A valid URL or IP address to connect to the server from
* 'sudo' privileges to the server
* You must have an account with the beta version of Central EnteroBase, since this is a different server to the regular Central EnteroBase (https://enterobase.warwick.ac.uk), you must register a new account at: https://35.246.24.128:5566/auth/register. (NOTE: this account will only exist for the duration of the beta test)
* You must be logged into the account that you would like to be the administrator of your Local EnteroBase. This account will be regarded by the Warwick EnteroBase team as the point of contact for your Local EnteroBase. This login can be performed at: https://35.246.24.128:5566/auth/login.

.. figure:: ../images/central_enterobase_user.png
   :align: center
   :alt: Central EnteroBase User Registration Form

   **Fig. 1 - Central EnteroBase User Registration Form**

Minimum System requirements
============================

* 4 CPU Cores
* 32GB RAM

  * Ideally your system RAM should be slightly more as systems may dedicate a small portion of RAM to other processes

* 40GB Free Storage Space

Automatic vs Manual Installation
=================================

There are two available options to install Local EnteroBase. Manually, or using an installation script. The script has been tested on the following distributions: Ubuntu 18.04, Ubuntu 20.04, Fedora 32, Fedora 33, CentOS 7, CentOS 8, Debian 9, and Debian 10. The automatic installer is simpler to get started with but if you are not using any of the listed distributions, please refer to the manual installation instructions. if you run into any issues, please refer to the manual installation documentation, or contact the beta testing team.

Additional Information
=======================

* If you would like to test multiple instances of the singularity container running, you would need an additional VM for a separate instance of NGINX to run.
