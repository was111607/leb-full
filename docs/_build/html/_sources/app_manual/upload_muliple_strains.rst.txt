Upload Muliple Strains
----------------------

* This options allows the user to enter one or more strains metadata along with the read files
* The user can write the metadata inside the table or import the metdata from a text file
* The user can select a folder which contains the read files and automatically it imports the paired read files for each strain and creates a row for it.
* The user has the option to export these data to a text file which he can edit and import the modified file.

.. figure:: ../images/upload_multiple_strains.png
   :alt: Upload Muliple Strains

   **Upload Muliple Strains dialog**

Notes
^^^^^

* This option is not completely implemented.
* It is needed to use the Zhemin API for source and location
    * This requires developing Warwick EnteroBase API functions
* I have implemented importing the data from a text file which contains the metadata but it needs more testing
* Submitting strains metdata and uploading read files are not implemnetd yet but we can modify and use the functions which are used to upload single strain.

