Files upload test
-----------------

* Inside the system configuration form (Fig. 1), there is a “Warwick temporary token” field, local adminstrator should set this value using the token whihc he has received by email. Then local adminstrator should select “File Uploads Test” option then click “Start” button (Fig. 2).

* After submitting the upload test results, the registration request will be verified by Warwick EnteroBase administrator.
* Once the registration request has been approved, local administrator  will receive an email which will contain the client id and a client password which will be used to authorize the communication between the local installation and the Warwick EnteroBase. 
* Local administrator needs to update your system configuration using these values and restart the application. At this stage, the system is ready and can be used.


.. figure:: ../images/configuration.png
   :alt: Client Registration Form

   **Fig 1 system configuration form**

.. figure:: ../images/test_upload.png
   :alt: Files upload test form

   **Fig 2 Files upload test form**

Notes
======

* We should use different database user as using postgres user is not recommended. This can be done in the automatic installation script.
* Using "Warwick client id" instead of using “Warwick client password” field, this is can be used to authorize upload files test.
* Instance name and icon should be removed. The instance name should be synchronized with the registration form.

